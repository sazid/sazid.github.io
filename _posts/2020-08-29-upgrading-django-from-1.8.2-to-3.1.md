---
---

At [Automation Solutionz](https://automationsolutionz.com), we've been
using Django 1.8.2 for almost the past five years! Without upgrading.
While deploying to a new server instance, I found out that, due to a
built-in Django bug, file uploads through forms do not work when using
Python >= 3.7. So, I quickly updated the Django version in that server
to 1.8.19 and everything was fine again. That's when I thought, why
don't we just upgrade to Django 3.x? Surprisingly, I was able to
upgrade the whole server to Django 3.1 within a single day, obviously
working almost non-stop past the usual working hours, except for
prayer and lunch break.

On a side note, our server does not rely on Django specific code that
much (except for routing and views). Models are very rarely used,
almost all the queries are either hand-written or generated by our own
defined functions. Needless to say, since we're not utilizing models,
we also do not use Django forms. Even our user management system is
custom built. Do not ask why... that's the state I found the project
in, when I joined. Well... even the whole stack was in a really bad
shape, which I had to improve. I'll go over that in another post
maybe.

Next morning, I started working on upgrading the whole system. A few
points I took before upgrading:

- Do not update existing libraries unless absolutely required (does
  not work with newer django version). There was an exception though,
  which you'll see.
- Do not change behavior of existing code in the middle of the upgrade
  process.
- Start with the next lowest django version and work my way upto the
  latest version one-by-one, unless there are no changes that'll break
  our code in between. Do not consider patch releases in between, only
  the final patched version. For example, if there are three versions:
  2.0.1, 2.0.2, and 2.0.3, try upgrading to 2.0.3.
- Reorganize files that are independent of the server code (scripts
  for database operations, image compression, etc.)

At each stage of the Django upgrade (from one version to another)
process, I ran a series of tests to make sure all the core components
are working correctly. Specially, the API end points, file
upload/downloads, browser caching and a few others.

The first thing I did was, reorganize the independent scripts that
were scattered around the root of the server directory. Then, I went
ahead and replaced `simplejson` with Python 3's built-in `json`
module. This is the only exception that I mentioned in the first
point. However, I quickly found out that `json` was used as a variable
name in many many places. I used Visual Studio Code's Find and Replace
to replace all those instances with `json_result`. This was a quick
change.

Next, I read through the changes in between 2.0 to 2.2 to see if I can
jump straight to 2.2. And yes, I could jump directly since the changes
that are required by 2.0 is the same as required if I upgraded to 2.2
(I'm obviously talking about our own codebase only. Make no mistake,
there were quite a lot). So, I went ahead and upgraded the version to
2.2. The few changes I had to keep an eye on were:

1. Change import of `reverse`.
    ```python
    from django.core.urlresolvers import reverse
    ```
   to
    ```python
    from django.urls import reverse
    ```
2. Upgrade Djagno Rest Framework to latest version (3.11.0).
3. `MIDDLEWARE_CLASSES` are deprecated in favor of `MIDDLEWARE` in
   `settings.py` file.

It seems that Django 2.x deprecated the
`django.shortcuts.render_to_response` function in favor of the
`django.shortcuts.render` function. So, I went ahead and started
replacing all instances of `render_to_response` in our codebase. This
was the most time consuming task in the whole processes. Since,
hundreds of view functions we created used it. Thankfully, I knew a
bit of regex and used the excellent regex support for Find and Replace
in VS code. Using capture groups, I was able to relocate the
parameters correctly for the render function. Some of the regex I had
to use are:

```
render_to_response\(['"](.*)['"],\s(.*)\{\}\)

render_to_response\(['"](.*)['"],\s(.*).*\)

render_to_response\([\s\n]*['"](.*)['"],[\s\n]*(.*),[\s\n]*.*\)

render_to_response\(['"](.*\..*)['"]\)

render_to_response\(['"](.*)['"],\s(.*)\)
```

Each of the capture groups `(...)` could be used in the Replace box
like: `render(request, $1, $2)` where `$1` and `$2` are the first and
second capture groups respectively, and they'll be substituted for the
values captured.

This concluded the upgrade from 1.8.2 to 2.2.15. Then I went ahead and
upgraded to 3.0, only to realize that there's not much change in
between 3.0 and 3.1 ...and I upgraded the Django version to 3.1. I had
to make two changes.

1. Change the database engine to `django.db.backends.postgresql` from
   `django.db.backends.postgresql_psycopg2`.
2. Since we were on 1.8.2, we used to use the regular expression based
   `django.conf.urls.url` which was removed completely in 3.x. But,
   the same functionality was available through `django.urls.re_path`.
   This warranted a replace of all the `url(...)` function calls with
   `re_path(...)` function calls.

And, that's it! The whole server was upgraded from 1.8.2 to 3.1 in a
single day! After this, the changes were pushed to our QA server to
test for any other issues. I'll probably go over the stack later on.