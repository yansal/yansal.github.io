---
title:  "GitHub user comments"
date:   2023-06-23 00:00:00
---
There are a lot of great discussions happening on GitHub issues. But it can be hard to keep up with all these conversations, especially on big projects.

It would be great to be able to list comments from a few key users, for example [Russ Cox](https://github.com/rsc) on the Go project, [Dan Abramov](https://github.com/gaearon) on the React project, [Andrew Gallant](https://github.com/BurntSushi) on the Rust project, etc.

As far as I know, there's no such feature in the GitHub interface. However, it can be done via the GitHub GraphQL API: [the User object does have an issueComments field](https://docs.github.com/en/graphql/reference/objects#user).

[So I developped a small application to do that](https://gh.yansal.com).

TODO: insert demo gif

The application is extremely basic: sign in with GitHub to authenticate the GraphQL request, enter the name of the GitHub user for whom you want to list comments, and _voilà_, all the issue comments from this user are listed (accross all repositories on GitHub, including private repositories the authenticated user has access to).

## Implementation 
I'm more of a backend person, so the GitHub request is done by the server. Note that GraphQL queries on GitHub can take seconds to run, so it's more pleasant to display some sort of progress indicator to the user. I opted for a 99% backend solution.

The GraphQL query is displayed to the user. While the request is in flight, we flush emojis to the browser every 100ms (thanks to [/net/http#Flusher](https://pkg.go.dev/net/http#Flusher), credit to [https://robpike.io/](https://robpike.io/)). Hopefully the user understands that everything is OK, we are just waiting for GitHub to serve the request. Once we receive the response from GitHub, we send the following HTML data to the browser.

```html
<script>document.body.innerHTML=""</script>
```

This is the only line of frontend code in the application. It erases the content of the page. Once it's done, we send the rendered data to the browser.

I think it's fun that the combination of flushing data from the server + clearing the HTML allows to give a touch of dynamism to the application, with only one HTTP rountrip between the browser and the server, and only one line of frontend code. If you are aware of other techniques to achieve that kind of result, let me know!

The code is available at [https://github.com/yansal/gh-graphql-issuecomments](https://github.com/yansal/gh-graphql-issuecomments).