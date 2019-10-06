---
title: inject you a dependency for great good
layout: post
date: 2019-08-23
author: Jacob
---

It's usually pretty to easy write tests for pure logic functions, but it's often the case that the code we're trying to test wants to make calls to external collaborators -- databases, API endpoints, filesystems, clocks, random number generators, or global state. Testing with real collaborators can be slow, brittle, and just generally a pain, and often gives rise to indeterminate behavior or has side effects on other tests.  

Dependency injection facilitates replacing real collaborators in production with more testable collaborators in test.  Given some core object representing the tool or server, you can inject these dependencies into it at the time of its initialization, whether in main or in tests.  If there's no such core object, and you're struggling to test-drive your new code, or get some legacy code under test, then maybe it's a good time to try DI.

Say you're adding tests to an API endpoint that makes a library call to a look up data from a datastore.  The existing code calls a library function directly in the handler for the endpoint. 

```
func SomeEndpointHandler(){
   //...
   value, err := datastoreLibrary.Find(key)
   //...
}
```

Try calling the handler from a test, and it blows up when it can't connect to the data source.  One option is to connect up to the VPN and add 15 lines of setup so that the connection succeeds and 15 lines of teardown to put the datastore back the way it was before the test.  Now start planning for how to make sure that the datastore returns a predictable result every time you run the test.  Not cool.  

You could also run a local instance of the datastore, possibly in a container, but now you have to maintain that test setup and make sure that it continues to realistically represent the production data store as the system evolves over the coming weeks and months.  Each test will need to run error-prone setup and teardown to put it in the pre-requisite state, which itself is not subject to test verification.  Also not cool!  And it's slow to run. And how many different collaborators are you willing to do that for?

Or you can refactor to use dependency injection.  Here's how:

1. Introduce an interface to represent the collaborator.  In this case we'll call it `Datastore` and give it a method called `Find` taking the same arguments the library call and returning the same types:

```
type Datastore interface{
   Find (string) (string, err)
}
```

2. Introduce the core object as the injection point.  Attach your existing endpoint handler to it, and replace the library call with a call to the injected dependency.  Here we create a new struct called Server with a `Datastore` field:

```
type Server struct{
   datastore Datastore
}
 
func (s Server) SomeEndpointHandler(){
   //...
   value, err := s.datastore.Find(key)
   //...
} 
```

3. Now we can introduce a new struct to represent the production version of the dependency, wrap the existing library call in the interface method, and inject the dependency at startup.  Here we introduce `realDatastore` to implement `Datastore` and inject it in `main`:
 
```
type realDatastore struct {}

func (realDatastore) Find (string) (string, err){
   return datastoreLibrary.Find(key)
}

func main(){
   //...
   s := Server{datastore: realDatastore{}}
   //...
}
```

4. In the unit tests we can use a test-double implementing the same interface.  In this case, we can test `SomeEndpointHandler` using a test-double instead of a `realDatastore`.  Here, `fakeDatastore` is guaranteed by the compiler to receive the exact same call that the `realDatastore` would and to return the exact same types: 

```
type fakeDatastore struct {}

func (fakeDatastore) Find (string) (string, err){
   return "foobar", nil
} 
```

In the happy path tests, we call `SomeEndpointHandler` on a `Server` injected with a `fakeDatastore`:

```
func TestSomeEndpointHandler_OK(){
   //...
   s := Server{datastore: fakeDatastore{}}
   s.SomeEndpointHandler()

   //assert the expected result based on the known return 
   // value of `foobar` from the datastore Find call 
}
```

You can also easily verify the behavior of the handler when the datastore call raises an error or returns a weird value by implementing tests similar to `TestSomeEndpointHandler_OK` above, initializing `Server` with a `fakeErrorProneDatastore` or a `fakeWierdoDatastore` like these:

```
type fakeErrorProneDatastore struct {}
func (fakeErrorProneDatastore) Find (string) (string, err){
   return "", errors.New("uh oh")
}
```
```
type  fakeWierdoDatastore struct {}
func (fakeWierdoDatastore) Find (string) (string, err){
   return "weird value", nil
}
```
There is a fair amount of duplication with all those fake datastores.  I'll show a handy way to tackle that in [another article](https://wwwin-github.cisco.com/jchampne/NotesOnCode/wiki/dry-up-your-doubles).
