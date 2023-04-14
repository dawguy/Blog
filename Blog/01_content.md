# Blog Creation

https://github.com/dawguy/Blog-Angular

Goal of this project is to create a blog to share travels, and convert the .md files I've been writing into something more professionally sharable.

I've gotten the static pages out of the way and am now trying to determine how I want to handle dynamic pages.

## Content I'd like to post about

Technical topics. I think this one would be the most interesting to keep long term. As I'd 100% like to do more writing about what I've done, and even just write about some explorations of topics that I find interesting + share my results.

Travel. I'm planning to take some time off work to travel for a couple months. Having a place to share photos + stories would be nice. Though this might end up being better suited for facebook.

Projects. Having a way to organize + explain the projects I've built will hopefully both boost my creativity and be a good teaching source for anybody who wants to do something similar. Projects likely will be both walkthroughs of code and my thoughts as I'm building it. So there will be some curating after the fact.

Book reviews. Unsure if this is high on the list, but there are many books that I think more people should read. Effective Executive and Learned Optimism are 2 of my favorites.

## Why Not Wordpress?

Good question, and I don't have an answer other than that I want to do this in angular.

## Ideas on how to structure dynamic content

1. Dynamic, what is that?
Basic premise of this one would be to do all page content statically.

It would be nice to have a database or something similar for handling what's available in terms of recent posts and perhaps a blurb about them, but the posts themselves could be done all through html

Pros:
* Lots of customizability
* Quicker to implement

Cons:
* Sitewide layout changes will essentially require rewriting everything (or writing a tool that will understand how to do this for me)
* Less involved backend wise.
* Updates would require a redeploy.

2. Create WYSIWYG Style Content Manager

Basic premise is to do something that would more closely mimic what is done by wordpress or medium. 

<Side note. Writing that last sentence made me realize I should see how medium editing looks like. Editing on medium quite literally looks like a WYSIWYG editor. Really well made and looks easy to manage.>

Pros:
* Would be interesting to make
* Once made would be easier to keep a consistent page structure
* Page content could be used for other purposes as it would be stored somewhere
* Once made adding new posts will be easier.

Cons:
* The effort required seems misplaced. I'd rather be writing or working on something more novel compared to storing content in a database and worrying about how to display it.
* Page structure might be more limiting.

3. Give Up On Angular

Pros:
* Medium is probably easier.

Cons:
* There are things I'd like to try with angular + would be a good experiment for writing terraform deployments.

4. Using Medium For Visualizing Page And Scraping Or Rewriting In Content Format

Pros: 
* Don't have to bother with writing my own WYSIWYG
* Easy to cross post in both medium & my blog
* Parser would be fun to write as it looks like the edit mode for medium.com has a well defined structure.
* Content blocks would be pretty straight forward to save. 

Cons:
* Scraper could break at any time.
* A bit more annoying than just writing this stuff myself.
