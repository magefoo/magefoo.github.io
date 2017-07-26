---
layout: post
title: "Remove SID from URL in Magento"
date: 2017-07-26 10:39:30
image: '/assets/img/'
description: Removing SID from URL in Magento
main-class: quickie
color: #FF0000
tags:
- Magento
- Quickie
categories:
- Quickie
twitter_text:
introduction: "Why the SID is in URL in Magento and how to remove it."
---

Since I cannot really explain any better than the great folks over at Inchoo, I will link to their blog post and give a brief overview here just in case they ever decide to delete there page. [Remove SID query from Magento URLs](http://inchoo.net/dev-talk/remove-sid-from-magento-urls/)

### So why does the SID part appear in the Magento URL in the first place?

We will let Magento's own words tell you why this happens.

> The session ID is transfered in URL whenever there’s a change in the host name, so the session would be transfered to the next page.  Usually session ID is kept in the cookie, but cookies can’t be shared between different domains, so it has to be transferred in URL. This is Magento’s functionality, not PHP, so changing PHP configuration will not help.  Note, that SID will be added to URL only the first time new domain is encountered during the session.

What does this say? Essentally, if say, the base url you have set up starts with www, and you enter the domain through the non www domain, it will have a SID because it is a change in the domain, you are being redirected from non www to www.

This is why the SID only appears sometimes, and not all the time.

### So what is the fix?

Simply add a redirect rule in your apache or nginx config. If you are using www inside the base url, then you would create a rule like this:

For Apache:

```
RewriteEngine on 
RewriteCond %{HTTP_HOST} !^www\.yourdomain\.com$ [NC]
RewriteRule ^(.*)$ http://www.yourdomain.com/$1 [R=301,L]
```

For Nginx

```
if ($host !~* ^www\.) {
    return 301 $scheme://www.yourdomain.com$request_uri;
}
```

If perhaps you are not using www in the base url, then a rule like below:

For Apache:

```
RewriteEngine on 
RewriteCond %{HTTP_HOST} !^yourdomain\.com$ [NC]
RewriteRule ^(.*)$ http://yourdomain.com/$1 [R=301,L]
```

For Nginx:

```
if ($host ~* ^www\.) {
    return 301 $scheme://yourdomain.com$request_uri;
}
```

*Another plus about adding rewrites such as these, it allows the request_uri to stay attached to URL as well, so if you're having issues with requests being redirected to home page this fixes that as well*
