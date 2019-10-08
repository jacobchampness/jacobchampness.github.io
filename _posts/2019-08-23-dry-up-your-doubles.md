---
layout: post
title: dry up your doubles
date: 2019-08-23
author: Jacob
---

Dependency injection is a great way to test a system’s interactions with its external collaborators, like databases and REST APIs.  This approach is described in an earlier article ([inject you a dependency for great good](https://wwwin-github.cisco.com/jchampne/NotesOnCode/wiki/inject-you-a-dependency-for-great-good)).  

One rough edge in the way the approach was demonstrated in that article was that we ended up creating a lot of different single-use struct types for our various test scenarios.  To make this more concrete, let’s take the example of a method in which we need to make a call to an external datastore:

```
func (u Util) listTheData(id string) (string, err) {
	results, err := u.datastore.Find(is)
	if err == nil{
		return “”, errors.New(“Error getting data.”)
	}
	return strings.Join(results, “,”), nil
}
```

We’d like to test not only the happy path, in which we process a slice of several results, but also the error case, and the case in which we get an empty slice.  Pursuing the DI approach, we already factored out Util as a central object and injection site, and created an interface to represent the datastore and encapsulate calls on the real collaborator, so that we can use test doubles to simulate the desired behavior for each test case.  For the happy path, we might write:

```
type fakeHappyPathDatastore struct {}

func (fakeDatastore) Find(string) ([]string, err){
   return []string{”foo”,”bar”,”baz”}, nil
} 
```

Then our happy path test case will look like this:

```
func Test_listTheData(t *testing.T){
	t.Run(“happy path”, func(t *testing.T){
		u := Util{datastore: fakeHappyPathDatastore{}}
		r, e := u.foo(“xyz”)
		assert.NoError(t, e)
		assert.Equal(t, “foo,bar,baz”, r)
	})
}
```

Next we want to verify the behavior in case of an error from the datastore.  So we make a datastore test double that raises an error: 

```
type fakeErrorProneDatastore struct {}

func (fakeErrorProneDatastore) Find (string) ([]string, err){
   return []string{}, errors.New("d'oh!")
}
```

And now we can implement the test by adding another t.Run like this: 

```
func Test_listTheData(t *testing.T){
	t.Run(“happy path”, func(t *testing.T){ //…
        }
	t.Run(“error case”, func(t *testing.T){
		u := Util{datastore: fakeErrorProneDatastore{}}
		r, e := u.foo(“xyz”)
		assert.ErrorEqual(t, “Error getting data.”, e)
		assert.Empty(t, r)
	})
}
```

Next we want to test the corner case of an empty slice without an error.  This leads us to write the following test double to be used in yet another t.Run:

```
type fakeEmptySliceDatastore struct {}

func (fakeEmptySliceDatastore) Find (string) ([]string, err){
   return []string{}, nil
}

func Test_listTheData(t *testing.T){
	t.Run(“happy path”, func(t *testing.T){ //…
        }
	t.Run(“error case”, func(t *testing.T){ //…
        }
	t.Run(“error case”, func(t *testing.T){
		u := Util{datastore: fakeErrorProneDatastore{}}
		r, e := u.foo(“xyz”)
		assert.NoError(t, e)
		assert.Empty(t, r)
	})
}
```

We might also like to test a number of other corner cases, like: What if we get a slice of empty strings?  What about a slice of strings that already have comma in them? What about a slice of strings with only whitespace? That's going to be a lot of different struct types!  

Here’s a handy solution for that:  We’re going to write a general-purpose test double for `datastore` and parameterize its behavior such that each test can set up the behavior it needs.  Here’s the new test double:

```
type fakeDatastore struct {
	find func(string) ([]string, err)
}

func (f fakeDatastore) Find (s string) (string, err){
   return f.find(s)
} 
```

Note the addition of `find`, a func field, to the `fakeDatastore` type.  Now the interface method `Find` delegates to its receiver's `find`, to eliminate the other three structs and transform our test cases as follows:

```
func Test_listTheData(t *testing.T){
	t.Run(“happy path”, func(t *testing.T){
		ds := fakeDatastore{find: func(string) ([]string, err){
		   return []string{”foo”,”bar”,”bar”}, nil
		}}
		u := Util{datastore: ds}
		r, e := u.foo(“xyz”)
		assert.NoError(t, e)
		assert.Equal(t, “foo,bar,baz”, r)
	})

	t.Run(“error case”, func(t *testing.T){
		ds := fakeDatastore{find: func(string) ([]string, err){
		   return []string{}, errors.New("uh oh")
		}}
		u := Util{datastore: ds}
		r, e := u.foo(“xyz”)
		assert.ErrorEqual(t, “Error getting data.”, e)
		assert.Empty(t, r)
	})

	t.Run(“empty slice”, func(t *testing.T){
		ds := fakeDatastore{find: func(string) ([]string, err){
		   return []string{}, nil
		}}
		u := Util{datastore: ds}
		r, e := u.foo(“xyz”)
		assert.NoError(t, e)
		assert.Empty(t, r)
	})
}
```

Note too, the happy side-effect that now the relevant behavior lives right there in the test.  Finally, note the amount of duplication that still remains inside of Test_listTheData.  We can solve that too, and will do just that in an upcoming article.


