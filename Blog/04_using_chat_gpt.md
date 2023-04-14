# Chat GPT

Experiments with ChatGPT

## Using A File As Hard-coded Draft Post

```text
Using angular how would I make an http request that returns data in a format as seen in this code block?

export interface Content {
  type: string,
  lines: string[],
  orderId: number,
}
```

It responded with an http.get(), precisely what I asked for but what I wanted was a way to just insert a document I'm working on into the page. From this response I ended up deciding to just make a draft-post-service and edit in-line of it. No HTTP Get's required.

## Asking For Tests

```text
Could you write tests for this class?

import { Injectable } from '@angular/core';
import {Content} from "./content-block/content";
import {HttpClient, HttpErrorResponse} from "@angular/common/http";
import {catchError, map, Observable, throwError} from "rxjs";

@Injectable({
  providedIn: 'root'
})
export class PostServiceService {

  private postUrl = 'http://localhost:8080/posts';

  constructor(private http: HttpClient) { }

  getPost(postId: string): Observable<Content> {
    return this.http
      .get<Content>(`${this.postUrl}/${postId}`)
      .pipe(map(data => data), catchError(this.handleError));
  }

  private handleError(res: HttpErrorResponse | any) {
    console.error(res.error || res.body.error);
    return throwError(res.error || 'Server error');
  }
}


The Content interface looks like below

export interface Content {
  type: string,
  lines: string[],
  orderId: number,
}
```

It responded with a working test file (needed to unprivate 1 variable).

## More Copmlex Test Generation

```text
import { Injectable } from '@angular/core';
import {Post} from "./post/post";

@Injectable({
  providedIn: 'root'
})
export class DraftService {

  text: string;

  constructor() {
    this.text =
`
blog
Test Draft Post
---
text
Hello world! my name is david.
---
text
My other name is bloodisblue or dawguy.
---
image
abcdef.png
---
indent
This is what an indented div looks like.
---
code-clojure
(prn (str "I did" (inc 2) "lines here!"))
---
code-typescript
console.out.println("Yoyo");
console.out.println("ABC");
---
text
Ending with some text.
---
`
  }

  getDraftPost(): string[] {
    let blocks = this.text.split("---");

    console.log(blocks);

    return blocks;
  }
}
```

It gave a blurb:
```text
This is a TypeScript code for a DraftService class in an Angular application. The DraftService class is used to retrieve a draft blog post, which is stored as a string with a specific format. The getDraftPost() method of the DraftService class splits the draft post string into an array of individual blocks, separated by ---. The blocks are then returned by the method.

The Post class is not used in this code, but it is possible that it is used elsewhere in the application. The @Injectable() decorator is used to indicate that the DraftService class can be injected with other services or components, and the providedIn: 'root' option specifies that the service should be provided at the root level of the application.

Note that this code does not include any external dependencies or imports, so it may not compile or work as expected without additional code or configuration.
```

So I responded:
```text
Could you write a test for this angular service?
```

And it responded with a fully functional test again. Really convenient to not need to really fill out any of the dummy data. I did end up returning a Post object instead of list of lines, but it made testing the inbetween steps much easier.
```text
Sure, here's an example of how you could write a test for the DraftService in Angular using Jasmine:

vbnet
Copy code
import { TestBed } from '@angular/core/testing';

import { DraftService } from './draft.service';

describe('DraftService', () => {
  let service: DraftService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(DraftService);
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });

  it('should split draft post into blocks', () => {
    const draftPost = `
      blog
      Test Draft Post
      ---
      text
      Hello world! my name is david.
      ---
      text
      My other name is bloodisblue or dawguy.
      ---
      image
      abcdef.png
      ---
      indent
      This is what an indented div looks like.
      ---
      code-clojure
      (prn (str "I did" (inc 2) "lines here!"))
      ---
      code-typescript
      console.out.println("Yoyo");
      console.out.println("ABC");
      ---
      text
      Ending with some text.
      ---
    `;
    const expectedBlocks = [
      'blog\nTest Draft Post\n',
      'text\nHello world! my name is david.\n',
      'text\nMy other name is bloodisblue or dawguy.\n',
      'image\nabcdef.png\n',
      'indent\nThis is what an indented div looks like.\n',
      'code-clojure\n(prn (str "I did" (inc 2) "lines here!"))\n',
      'code-typescript\nconsole.out.println("Yoyo");\nconsole.out.println("ABC");\n',
      'text\nEnding with some text.\n',
      '\n'
    ];
    expect(service.getDraftPost(draftPost)).toEqual(expectedBlocks);
  });
});
In this test, we first create an instance of the DraftService using the TestBed utility from Angular. We then test that the service was created successfully using the toBeTruthy() matcher.

Next, we test the getDraftPost() method by passing in a sample draft post string and comparing the returned array of blocks with the expected array. We use the toEqual() matcher to compare the arrays.

Note that this test assumes that the getDraftPost() method returns an array of strings, rather than a single string as the original implementation suggests.
```
