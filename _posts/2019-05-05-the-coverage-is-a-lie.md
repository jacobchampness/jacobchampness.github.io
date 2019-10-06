---
layout: post
title: the coverage is a lie (or is it?)
date: 2019-05-05
author: Jacob
---

Unit test coverage is an interesting topic.  

At times in the past, individuals and organizations have fetishized high coverage numbers.  This is misguided, as high coverage can be attained without a single meaningful assertion in the test suite.

Here's an example in go.  Imagine a codebase like this:
```
import (
	"errors"
	"fmt"
	"io"
	"io/ioutil"
)

func f() error {
	getSomeResourceThatNeedsToBeClosed() //bug: the resource is never closed
	reachOutToExternalService()          //bug: call to external service fails
	interactWithMassiveSDK()             //bug: wrong call order
	doComplicatedLogic()                 //bug: error is not checked
	return errors.New("it didn't work")
}

func getSomeResourceThatNeedsToBeClosed() io.Closer {
	fmt.Println("getting some resource that needs to be closed")
	return ioutil.NopCloser(nil)
}

func reachOutToExternalService() int {
	fmt.Println("failed reaching out to some external service")
	return 0
}

func interactWithMassiveSDK() []string {
	fmt.Println("making what should be the second call on massive SDK")
	fmt.Println("making what should be the first call on massive SDK")
	return []string{}
}

func doComplicatedLogic() (int, error) {
	fmt.Println("doing some complicated logic which only works on February 29")
	return 0, errors.New("it's not February 29")
}
```

This test will bring it to 100% coverage without finding a single one of the latent bugs:
```
import (
	"testing"
)

func Test_f(t *testing.T) {
	f() //100% coverage, 0 assertions
}
```

In reaction, many have disregarded test coverage.  This is also misguided, since low coverage is unambiguously bad.  While it may well be the case that important assertions have been left out of a test suite yielding high coverage, it is certain that they have been left out of a low-coverage test suite.  

After all, deciding to ignore coverage in the above example really doesn't help anything does it?  

So the right way to think of coverage is that high coverage is a necessary but not a sufficient condition for a robust codebase.  High coverage can be a lie, but only if you're gullible.  Low coverage is brutally honest.
