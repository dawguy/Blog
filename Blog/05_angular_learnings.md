# Angular Learnings

## Providers
https://angular.io/guide/providers

Had an issue where my draft-service (and HttpClient to be precise) weren't loading. Reading up on this to help.

https://angular.io/guide/bootstrapping

The bootstrapping page adds a lot more context for making a metal map of this.

Essentially we start with app.module.ts which specifies what Module should be bootstrapped. By marking this as AppComponent, angular will then take the bootstrapped component and do dependency injection based on that.

## My mental map

Declarations: What is owned by the module. Everything can be owned by at most 1 @NgModule. If needed in multiple places, then the `imports` must be used.`

Imports: Pulls in a separate module to the DI system.

Providers: Seems like services don't necesasrily need to be attached to a module? Still don't quite understand this one.

"Component providers and NgModule providers are independent of each other."
"Register a provider with a component when you must limit a service instance to a component and its component tree, that is, its child components."
"[{ provide: Logger, useClass: BetterLogger }]""

Providers seem like they are 



Bootstrap: What to first load. My note: Typically use <router-outlet> for the contents.

---

## Another Fun Bug

Seems like retrieving text vs httpClient is harder than I expected.

```
https://github.com/angular/angular/issues/18586

    const opts = {
      headers: new HttpHeaders({
        'Content-Type': 'application/x-www-form-urlencoded',
        'Accept': 'text/html',
      }),
      responseType: 'text' as 'text'
    };

    return this.http
      .get(`${this.draftPostUrl}/${postId}`, opts)
      .pipe(map( data => this.draftService.getDraftPost(data)), catchError(this.handleError));
```


Seems like 'text' as 'text' is required for this to work.
