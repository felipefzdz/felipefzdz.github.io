---
layout: post
title: "Mock types that you want"
description: ""
category: 
tags: [tdd, unit testing, testing]
---
{% include JB/setup %}

There is a famous quote that says: Don't mock types you don't own (details [here](http://stackoverflow.com/questions/1906344/should-you-only-mock-types-you-own))

If we have a look into the more general question about what [we shouldn't mock](http://www.mockobjects.com/2007/04/test-smell-everything-is-mocked.html), we can read some interesting ideas:

1. "There isn't much point in writing mocks for simple value objects (which should be immutable anyway), just create an instance and use it." I agree with that, one of the main points about isolating your tests is speed, and calling real value objects is insignificant.
2. "Don't mock third-party libraries" The suggested approach is wrapping the code that calls the third party into a thin layer. I agree that that thin layer should exist and that it should be as thin as possible. Often, third party libraries APIs are clumsy and we can't control that, so it's better if we split our code. Let's seen an example.

I'm using [Mongo Java Driver](http://docs.mongodb.org/ecosystem/drivers/java/) to access to MongoDb. I don't have so many experience with that API, so maybe what I'm going to show can be done with more elegancy, but let's focus in the example.

	public List<Job> all() {
        DBCursor cursor = collection.find();
        List<Job> jobs = new ArrayList<>();
        while(cursor.hasNext()){
            jobs.add(jobAdapter.fromDBObject(cursor.next()));
        }
        return jobs;
    } 
    
This is the thin layer that wraps the third party library. I'm missing some kind of annotation system that can do the mapping for me (I'll have a look into ODM solutions in the future) or at least some API like this:

	List<Job> jobs = collection.find().with(adapter);
	
I think that the original code is worthy to be tested. In the post that we're reviewing, the author suggests relying on integration tests, but my intent is reducing integration tests to the minimun; they're slow and hard to set up.

If we decide to go with integration testing approach, we should note this:

> There are some exceptions when mocking third-party libraries can be helpful. You might use mocks to simulate behaviour that is hard to trigger with the real library, such as throwing exceptions. Similarly, you might use mocks to test a sequence of calls, for example making sure that a transaction is rolled back if there's a failure 

I decided to unit test that thin layer, so I need to stub (or mock if you prefer) types that I didn't own.

	@Test public void
    return_several_jobs() {
        given(cursor.hasNext()).willReturn(true, true, false);
        given(dbCollection.find()).willReturn(cursor);
        given(jobAdapter.fromDBObject(null)).willReturn(aJob().build());
        List<Job> jobs = new MongoJobs(dbCollection, jobAdapter).all();
        assertThat(jobs.size()).is(2);
    }

The test is ugly and it was hard to write. But this is the price that we pay for using APIs that we can't design. So my conclusion is that you should mock whatever you need is worthy, and not following that "Don't mock types you don't own" rule.