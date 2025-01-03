---
layout: post
title: "My Blogging Platform Experience So Far"
tags: en jekyll wordpress ghost
---
Happy New Year! It's not yet late to say this, and I'm happy I made it :)

I've been using Jekyll, a static site generator for this blog for over a month, so I'm thinking to conclude some experience and thoughts around it.

# Reason for choosing Jekyll
I didn't have experience hosting a blog before, and the main reason I chose to use Jekyll as the platform is because it's simple and free.

The biggest advantage of Jekyll is that it can be easily deployed via GitHub Pages. It's developed by one of the founders of GitHub, maybe that's why GitHub Pages supports Jekyll site hosting really well. As long as you have the `_config.yml` file correctly set, and your GitHub Pages repo follows the correct file structure, GitHub automatically deploys your blog once you enable it, no need to push any extra GitHub Action config file.

The post format is incredibly straightforward: a YAML front matter plus a Markdown content. It's perfect for someone like me who is used to Markdown writing.

# Jekyll Experience
Though Jekyll isn't a complex tool, it took me several days to feel comfortable with it. Mostly because I like to first trial and error on my own when learning a new tool, instead of immediately going to tutorials. (I know it's not the most efficient way but I like it to be more fun :p)

I chose to use the theme `Hyde`, because it reminded me of _Strange Case of Dr Jekyll and Mr Hyde_ which is one of my favorite books. But the theme was very basic, it doesn't even support the tagging feature. I did some learning to modify the HTML templates and adding a tag navigation, it was a good chance to practice HTML and Liquid coding, and would be a piece of cake for those who are familiar with frontend programming, but I'm not that interested in putting more time on it, so eventually I just simply went to another theme called `Tale`, which fulfills my current needs and is what I'm using.

I'm choosing a simple platform and theme on purpose, to stop myself from putting too much effort into playing around with flashy features. But I also received some feedback that the website is "too simple" to look professional and attract readers. Alright, I'll think about it, but later :p

I've also enabled Google Analytics, which is supported by Jekyll and worked well to make me aware that no one actually visits my blog XD Jokes aside, enabling it only requires adding one line `google_analytics: <measurement id>` in the `_config.yml` and the file `_includes/analytics.html` with the following content:
{% raw %}
```js
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?{{ site.google_analytics }}"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', '{{ site.google_analytics }}');
</script>
```
{% endraw %}
The measurement ID is provided by Google Analytics.

# Trying Out Other Platforms
## WordPress
I've also tried a bit on **WordPress** and **Ghost**, since eventually I might want features like subscription and comment which are easier to provide via dynamic site generators. 

**WordPress** is a well-known and mature platform for blogging, it has plenty of features and plugins, and is a must try when building a site. This time I tried AWS Lightsail to build a WordPress site, which worked like magic. Lightsail offers a 3-month free tier and foolproof configuration for Wordpress, which took me only 5 minutes. 

However, I got immediately overwhelmed by information when trying to choose a theme as most themes include too much blocks and contents that I don't need e.g. self-advertising. Eventually I got inspired by a Youtube video [Choosing The Right Theme for Your WordPress Site by LearnWebCode](https://www.youtube.com/watch?v=LwIAzz9sujk) and decide to explore more the native themes provided by WordPress later. 

Wordpress might be suitable for me to build a gallery for my own arts as I need features like media libraries, watermarks and a comment system, but for this blog, it currently feels like overkill.

## Ghost
I've also tried a bit on **[Ghost](https://ghost.org/)**, by running it locally, which went smoothly. Just installing Node, NVM and NPM, then running `npm install ghost-cli -g`, and the rest configuration can be done via the web UI. It's amazing for those who want to have their blogs as part of their careers and want to focus on creating content but not digging too much in technical stuff, as Ghost provides easy setup for subscriptions and membership. I've only tried about 10min, and already impressed by its well-designed UX. When I'm ready to run a server with a dynamic website, maybe I'll give it another try.

# Summary
For now, I plan to stick with Jekyll for some time while refining my site’s design and exploring more advanced themes and platforms. Blogging is as much about sharing ideas as it is about continuous learning, and these tools have certainly made the journey an exciting one.
