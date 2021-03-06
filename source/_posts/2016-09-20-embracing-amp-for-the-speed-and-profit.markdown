---
layout: post
title: "Embracing Accelerated Mobile Pages (AMP) for Speed and Profit"
date: 2016-09-20 11:30
comments: true
author: Shidhin CR
categories:
- AMP
- Performance
- Accelerated Mobile Pages
---

Over the past years, we have seen the rise of smartphones. Mobile phones and mobile browsers became more powerful as they have the hardware and processors in par with  desktops. Subsequently, this lead to a rapid growth mobile browsers usage over desktop browsers.

On the other hand, mobile browsers has a hidden cost -- called Performance.In terms of performance, they were always been compared with Native apps. The native apps had better performance because of the underlying OS level support. They could cache big chunks of data offline, hence wouldn’t suffer from network latencies. Mobile browsers/websites didn’t have this luxury and they have to bear the performance problems, always.
<!-- more -->
UI frameworks and libraries could use the latest technologies ( CSS Animations, VirtualDom and WebComponents ..etc ) to fight with native apps in terms of rendering performance. However, page loading performance remained as the biggest issue for mobile websites. Although, the HTTP2 protocol would be able to solve some of these issues, the web needs alternate solutions.

## Meet AMP

AMP -- Accelerated Mobile Pages -- is an open source initiative from Google to fix the slowness of mobile websites. AMP initially targeted only the publishing platforms, but sooner grown into all kind of websites. The AMP team in Google is actively working for enabling more support for E-commerce websites. You can find about that more [here](https://amphtml.wordpress.com/2016/08/22/getting-started-with-amp-for-e-commerce/)

[AMP Project](https://www.ampproject.org/) is a collection of components and guidelines from Google on building high performing websites. These components were developed for achieving better rendering and loading performance. Google also built tools for validating the AMP pages to make sure they meet the standards.

Once an AMP page meets the AMP html requirements, and passes the validations, Google can cache them and serve from their CDN. These caches are highly optimized for the fastest loading experience. Moreover, websites can forget about performance problems, as it's on Google's shoulder now.. :)

Though the AMP project was started for the publishing platforms initially, it is grown from there. As of now, the AMP website has enough components to build a web page for an e-commerce website. Ebay was the first website to use AMP html in their website and now they have built more than 2 million AMP pages. You can read more about their stuff [here](www.ebaytechblog.com/2016/06/30/browse-ebay-with-style-and-speed/)

## AMP in Namshi

We’re one of the top e-commerce website in MENA region. Similar to Ebay, we have seen the potential of AMP pages in e-commerce websites. AMP pages can play a vital role in the organic search results. No one would love to open a slow website even if it’s the first one in Google search results. Sooner, people will look for the AMP tag in Google results, than clicking on the first one.

As said, giving an ultrafast loading experience to the users was our main goal. We knew that AMP would be the right choice for this ( considering the traffic from organic search results ). At this time, AMP had all the ingredients for our requirements, and more importantly, we didn’t have to make many changes to the existing website.

We found out that the catalog pages are the best candidate for us to start with. First, because they are the most linked from organic search results. Second, AMP website had all the components required to build our catalog pages. We didn’t write a single JavaScript line of code to make it work!

## How we started

As explained earlier, we chose the catalog pages for AMP. Now, let’s recap the AMP requirements:

1. No custom JavaScript on the page
2. All CSS has to be AMP compliant ( you cannot use specific styles, no !important ..etc ) and should be inlined in the <head> tag.
3. Wherever applicable, AMP component should be used. For example, amp-image, amp-iframe ...etc

All these were possible in catalog pages. We started by creating an amp version of the catalog page template and linked it with the non-AMP page by providing these meta tags.

```html AMP-html
<meta href=”canonicalUrl” rel=“canonical” />
```

```html Original-html
<meta href=”ampUrl” rel=“amphtml” />
```

Generating and serving the AMP html was straightforward in our mobile website. The only change was to add a new route prefixed with “/\_amp/” to serve the catalog AMP html.

Now, we took the basic AMP template from [here](https://www.ampproject.org/docs/get_started/create.html) and started the validation process. AMP validation in development is really simple; Just add the ”#development=1” in the url, and AMP will show all the errors and warning in the browser developer console.

This was the starting point. Now all we had to do is to add new markup/components and keep validating. By the end of the day, we built the complete catalog page structure with available AMP components.

The next big thing was the styling. As per the AMP requirements, we had to inline the whole CSS required for the catalog pages. These are the two tools came in handy for us:

- [Purify CSS](https://github.com/purifycss/purifycss)
- [Chrome browser extension for extracting used CSS](https://chrome.google.com/webstore/detail/css-used/cdopjfddjlonogibjahpnmjpoangjfff)

Once we had the required CSS generated, the final step was to integrate it with the build process. It was not a big deal, as we had to add a new gulp task to generate the AMP html and inline the CSS with it.

Last but not least, building the structured data for each catalog pages. AMP required structured data for validation -- but dropped later ( [see this](https://support.google.com/webmasters/answer/6211453?hl=en) ) -- and this was pretty much an easy step. The structured data is processed and included at runtime based on the catalog page content.

## Main Challenges

Here are the main challenges faced during the development.

- **No support for custom JavaScript:**
AMP components are optimized for rendering performance. That’s why they restricted the usage of any other JavaScript code on the page. This wouldn’t be a problem if we were building everything from scratch, but it can be challenging when trying to build an AMP version of an existing page.

- **Some components are still missing:**
Initially AMP was more focused on content publishing platforms, hence most of the components are built for that. However, the project is growing and they have enough components for building a catalog page. However, while developing the AMP pages for our catalog pages, we wanted to use the TABS component, but it was not available. Therefore, we couldn’t build the same UI of our normal catalog page in AMP.

- **Optimizing existing CSS for AMP requirements:**
AMP has some restrictions on the CSS that should be used on the page. This can be tricky when converting an existing page to AMP, as you might have to manually remove style rules to make the validation pass. To know more, see the AMP styling [requirements](https://www.ampproject.org/docs/guides/responsive/style_pages.html)

- **Analytics:**
This was the biggest problem for us. We were using GTM ( Google Tag Manager ) for our website, but, AMP does not have any support for GTM scripts. Because of this, we couldn’t use our existing GTM script for tracking the AMP pages.
As of now, AMP analytics component supports multiple vendor configurations. The whole list can be found [here](https://github.com/ampproject/amphtml/blob/master/extensions/amp-analytics/0.1/vendors.js).For the time being, we use only the Google analytics component in our AMP pages.

## The RESULT was amazing

Less than 6 months ago, Google started showing AMP page in the “Top stories” news carousel. The news carousel was a special section on top of the search results, and shows only the AMP pages for articles and news.

Last month, Google announced the AMP support for all the other type of web pages. This means, Google will start indexing all type of AMP pages, and will display them with an AMP tag ( ![amp-fast-logo](/images/posts/AMP_logo.png) ) in search results. The lighting fast symbol implies that the page is actually cached in Google CDN and served as fast as possible.

As per Google,

> we’ve seen incredible global adoption of AMP that has gone beyond the news industry to include e-commerce, entertainment, travel, recipe sites and so on. To date we have more than 150 million AMP docs in our index, with over 4 million new ones being added every week. As a result, today we’re sharing an early preview of our expanded AMP support across the entire search results page --not just the “Top stories” section.

To support a better user experience for AMP in search results, Google provided an early preview demo page. This is actually useful for developers to test their AMP pages before Google go live with AMP search result. So far, we have most of our catalog pages indexed, and the early AMP search results preview looks amazing.

{% img center /images/posts/namshi-amp-demo.gif 'Namshi AMP Demo' 'Namshi AMP Demo' %}

This is the kind of page loading experience we wanted to provide to our customers, and AMP was the right choice for us. We’re pretty happy with the outcome and very much excited to see the actual results once Google rollout AMP search.

If you want to see the AMP preview, go to **[g.co/ampdemo](https://g.co/ampdemo)** (in a mobile browser) and try the search query “namshi lacoste shoes”. We want your feedbacks!

## Future Work

Currently we have only the catalog pages AMP-fied. We would like to try it out on more pages -- once more components are available.

Moreover, we need to make the transition from AMP pages to the regular pages as smooth as possible. The best plan would be to use a service worker to cache all the assets from the website. The service worker can be installed either using a [amp-service-worker component](https://ampbyexample.com/components/amp-install-serviceworker/)  or [declaratively](https://philna.sh/blog/2016/08/17/install-a-service-worker-declaratively/) with a link tag. More importantly, this will open doors to more cool experiments like push notifications, app-shell caching ..etc. The AMP+SW combo seems to work well, and if you want to see a working example, checkout the [AMP website](https://www.ampproject.org/).

We also want to add HTTP2 and Server push to our mix and experiment with them. These are all the features we’re planning to add to the current setup.

Finally, Thanks to all the people who make the web awesome !
