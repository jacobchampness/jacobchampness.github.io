---
layout: post
title: let it crash
date: 2019-07-12
author: Jacob
---

In go, we often find ourselves reflexively checking and handling each and every error that may be returned from each and every function we call.  Sometimes it's better to let some of them slide.  

If you write a line of code, you should test it.  If you don't test it, you must not care if it gets mangled in the future.  If you don't care if it gets mangled, then you might as well go ahead and remove it now.  At least that way there's one less LOC.

But if it's important enough to write and test, then it's worth thinking about that code's cyclomatic complexity.  The following method has cyclomatic complexity of five.  That's five possible paths through the code and a minimum of five test cases to be written.

```
func (s sesReportEmailer) generateEmail(report []byte, date string) (string, error) {
	emailBody, err := s.partCreate.CreatePart(body)
	if err != nil {
		return "", err
	}

	_, err = emailBody.Write([]byte(fmt.Sprintf("Attached is the toplines report for %s.", date)))
	if err != nil {
		return "", err
	}

	attachment := textproto.MIMEHeader{}
	file, err := s.partCreate.CreatePart(attachment)
	if err != nil {
		return "", err
	}

	_, err = file.Write(report)
	if err != nil {
		return "", err
	}

	return s.buf.String(), nil
}
```

But here's the thing.  All those early returns mean basically the same thing in terms of the purpose of the method, which, as the name indicates, is to generate an email.  The early returns are all just different indications the program can't generate that email, and unless there's some failover contingency to be performed down the call stack which is going to depend on the specific error, we're interested only in the fact that there was an error, and not in the specific error which occurred.  

Beyond that, early returns in error cases are often intended to short-circuit logic that would otherwise panic if execution of the function proceeds.  An example of this is the `emailBody.Write` call above-- the hope is that if `err` is not nil following the call to `s.partCreate.CreatePart`, then `emailBody` will not be nil and the call to `emailBody.Write` won't panic.  Note that that's not in any way guaranteed to be the case and depends entirely upon the adherence of the author to convention.

So when we decide to let errors slide, we must be prepared to cope with panics which, in fact, even the obsessive error-checker should also be prepared to handle.

Check out the refactored version of `generateEmail` below.  It has a cyclomatic complexity of one, with a minimum of one test case to be written.  Additionally, it is actually more robust than the first version in that it is guaranteed not panic due to nil pointers.

```
func (s sesReportEmailer) generateEmail(report []byte, date string) (string, error) {
	defer func() { recover() }()

	emailBody, _ := s.partCreate.CreatePart(body)
	emailBody.Write([]byte(fmt.Sprintf("Attached is the toplines report for %s.", date)))

	attachment := textproto.MIMEHeader{}
	file, _ := s.partCreate.CreatePart(attachment)
	
        file.Write(report)

	return s.buf.String(), nil
}
```

What we did there was simply to explicitly ignore all the errors that are raised within the method and defend against panics with the deferred `recover` call.  This approach is not going to work in every case, but it's worth keeping in mind whenever a function begins to be heavily festooned with error checking and handling.
