---
layout: post
title: testing for resource cleanup with dependency injection
date: 2019-05-20
author: Jacob
---

Testing can be hard.  Sometimes we just have to structure things differently.  

Here's an example where we need to clean up a resource:
```
import (
	"fmt"
	"io"
	"io/ioutil"
)

func f() error {
	mustBeClosed := getSomeResourceThatNeedsToBeClosed()
	defer mustBeClosed.Close()
	return nil
}

func getSomeResourceThatNeedsToBeClosed() io.Closer {
	fmt.Println("getting some resource that needs to be closed")
	return ioutil.NopCloser(nil)
}
```

And here's a first shot at testing it:
```
import (
	"testing"

	"github.com/stretchr/testify/assert"
)

func Test_f(t *testing.T) {
	assert.NoError(t, f())
}
```

This test passes, and makes a reasonable assertion, that `f` won't return an error.  It even achieves [100% coverage](https://wwwin-github.cisco.com/jchampne/NotesOnCode/wiki/the-coverage-is-a-lie-(or-is-it%3F)).  However, it asserts nothing about whether `mustBeClosed` is closed and as a result, is vulnerable to a regression in the future.  

To see this, imagine that the `mustBeClosed.Close()` call is inadvertently removed (while resolving a merge conflict, say) at some point in the future.  No test will complain.  We will go ahead and deploy, and only when the resource leak crashes this or some other service in production will we know there's a problem.  

The way this code is currently structured, there's no good*, easy way to verify that `mustBeClosed` is closed. 
 Let's look at a way to restructure it so that we can assert that `mustBeClosed` is closed.

Here, we introduce `service` as a central object for our system, having a `closerGetter` member, and we give `f` a `service` object receiver.  During startup a `service` object will be instantiated with a `closerGetter`.  We can do the same in tests, such that we can make assertions about subsequent calls on the value returned from calling `getSomeResourceThatNeedsToBeClosed`.  Let's look at that below:

```
import (
	"io"
)

type service struct {
	getSomeResourceThatNeedsToBeClosed closerGetter
}

type closerGetter func() io.Closer

func (s service) f() error {
	mustBeClosed := s.getSomeResourceThatNeedsToBeClosed()
	defer mustBeClosed.Close()
	return nil
}
```

In the updated test below, we introduce `fakeCloserGetter` and inject it into the instantiation of the `service` object.  `fakeCloserGetter` closes over the local state of `resourceClosed` such that we can observe side-effects upon it.  Now when we call `f` on our `service` instance, `s`, we can go on to make an assertion that `resourceClosed` is true, proving that the `Close` was called on resource.  

```
import (
	"io"
	"testing"

	"github.com/stretchr/testify/assert"
)

type fakeCloser struct {
	close func()
}

func (f fakeCloser) Close() error {
	f.close()
	return nil
}

func Test_f(t *testing.T) {
	resourceClosed := false
	closer := fakeCloser{func() { resourceClosed = true }}
	fakeCloserGetter := func() io.Closer { return closer }
	s := service{getSomeResourceThatNeedsToBeClosed: fakeCloserGetter}
	s.f()
	assert.True(t, resourceClosed)
}
```

Imagine again the scenario that the `mustBeClosed.Close()` call is inadvertently removed.  Now `Test_f` will immediately fail.  We are not going to be paged when our leaky service crashes. We don't have to write a monitor for open resources.  We don't need a dashboard widget for open resources.  

After conversion to a central object with injected dependencies, the code is admittedly less concise.  But this one-time refactoring will allow us to easily inject other dependencies as the codebase matures and support making assertions about the interactions with those dependencies with a minimum of additional complexity.

(*) monkey-patching is easy, but not good.  Still better than not testing though....