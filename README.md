<div align="center">

# Learn `GitHub Pages`



[![HitCount](https://hits.dwyl.com/dwyl/learn-github-pages.svg?style=flat-square)](https://hits.dwyl.com/dwyl/learn-github-pages)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat-square)](https://github.com/dwyl/phoenix-chat-example/issues)  

Learn how to use GitHub Pages to publish your website! It's easy, free and you have precise (version) control over your content!

</div>


# What? Why? 📄

So you've built your website using X framework.
And you want to share it with your friends!
You've probably seen many cloud services 
that provide you with a way to host your site for free - 
[Netlify](https://www.netlify.com/), 
[Firebase](https://firebase.google.com/),
[Surge](https://surge.sh/)
or even [AWS](https://aws.amazon.com/)!

Even though *some options are free*,
others *aren't*.
Regardless,
using has many advantages 
and it's **extremely practical**
if you're already developing your website 
on this platform.

So why should you use Github Pages?
It's a great way to communicate
with **like-minded people** by showcasing your projects,
thus contributing to the **Open-Source community**.

- **It's free**. This goes without explanation.

- **Custom domain support**. 
By default, sites deployed on Github Pages 
are deployed to `https://<your_account_name>.github.io/<repo_name>`.
But you can *buy* a domain for yourself
and [use it for yourself](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)!

- **HTTPS support**. 
If you've ever deployed a website on-premises,
you may know that getting a certificate
so your website runs on HTTPS
is a *cumbersome process*.
With Github Pages, 
you can enforce HTTPS 
with a click of a button!

<p align="center">
<img width="695" alt="https_support" src="https://user-images.githubusercontent.com/17494745/222492407-a53a0343-3e3e-45de-aefd-5edc860394de.png">
</p>

- **It's seamless**.
By setting up a simple 
[Github Action pipeline](https://github.com/features/actions),
you can have your CI being deployed automatically!

- **`Jekyll` automatically supported**.
[`Jekyll`](https://jekyllrb.com/)
is a Ruby-based site generator 
which will create HTML and serve our files for us.

> **Note**
>
> It's essential to be "crystal clear" about this: 
> **Microsoft *OWNS* GitHub**. 😢
> Therefore using GitHub Pages as a publishing tool 
> means you are trusting Microsoft to:
> 
> a) Keep your data and privacy safe.
> 
> b) Not censor or hide your content because they disagree with your views/beliefs.
> 
> We have no reason to suspect/believe that Microsoft is going to delete/censor our content
> and given that we can store a complete copy of our data in a "safe place" using 
> [`dwyl/github-backup`](https://github.com/dwyl/github-backup),
> we feel the risk is acceptable.


# Who? 🧍‍♂️

This guide is meant as both 
an **_internal_ reference** for us at **`@dwyl`**
and a **_fully_ Open Source resource** 
that _anyone_ can read and learn from.

Publishing sites publicly is in the interest
of **everyone**. 
And that should include you!


# How? 💻

Luckily for you, 
integrating a deployment pipeline
to your repo to Github Pages
is fairly simple!
It's one of the perks of using it!

Github Pages **needs a source to pull the files from**.
This can be a specific *folder* 
in a given *branch* of the repo.


<img width="1044" alt="settings" src="https://user-images.githubusercontent.com/17494745/222521320-94cd9c0e-b5ca-48b2-800c-327ef031ebe8.png">

Regardless, 
once we set the branch and the folder,
Github Pages will perform a
`Github Action` to deploy to the default domain -
`https://<your_account_name>.github.io/<repo_name>` 
(you can change the domain to a custom one).

Take a look at the picture below.
This action is *automatically executed*
after hitting the `Save` button on the
`Settings > Pages` page of the repository.
In this case,
we are using the **root files** in the 
**`gh-pages` branch** 
to be served as the website.

<img width="1224" alt="image" src="https://user-images.githubusercontent.com/17494745/222522110-6d028bd5-bc25-4577-8669-3aeba6aa6e58.png">

## Automating this process

Depending on the framework/language you are using,
you can simply create a *release bundle* 
and manually upload it to the repo on a different branch
and have it served in a Github Page.
However, this process is *time inefficient*.
We could (and *should*!) leverage 
**Github Actions**
to automatically build and deploy our website to Github Pages.

You can check the quickstart guide
for Github Actions in https://docs.github.com/en/actions/quickstart.

But, in layman's terms,
you need to have a `.github/workflows` directory in your repo,
and create `*.yml` files within it.
**Each of these files will pertain**
**to the configuration of the workflow(s)**
**that will be executed**.

Here's a practical example
of a Github Action workflow file
to deploy to Github Pages.

```yml
name: deploy to Github Pages
on:
  push:
    branches: [ main ]


permissions:
  contents: write
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      
      - name: Install Flutter
        uses: subosito/flutter-action@v2
        with:
          channel: 'stable'

      - name: Install dependencies
        run: flutter pub get

      # See stackoverflow.com/questions/74164386/flutter-web-shows-blank-page-on-github-deployment.
      - name: Install and Build 
        run: flutter build web --release --web-renderer html

      - name: Deploy to Github Pages
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          branch: gh-pages # The branch the action should deploy to.
          folder: ./build/web # The folder the action should deploy.
```

> **Note**
>
> This code is originally from 
> [`dwyl/app`](https://github.com/dwyl/app/blob/4ae03e4a0afefdf42a5af20c86fbf1790cbd57e5/.github/workflows/deploy.yml).
> We *highly* encourage you to check it out,
> and see the [actions](https://github.com/dwyl/app/actions) be executed! 😄

Let's break the code down.

This workflow is only executed 
when a push commit is made to the `main` branch,
and has permissions to *write* on the repo.

A `job` called `"build-and-deploy"` 
is run on an `ubuntu` container, 
and has several steps.

- the code is [checked out](https://www.atlassian.com/git/tutorials/using-branches/git-checkout#:~:text=The%20git%20checkout%20command%20lets,new%20commits%20on%20that%20branch.)
(copied) from the repo into the container.

- since the project is 
[`Flutter`-based](https://flutter.dev/),
we need to install `Flutter` 
and fetch the dependencies. 
This is made by using the 
[`subosito/flutter-action@v2`](https://github.com/marketplace/actions/flutter-action)
to install Flutter
and running `flutter pub get`
to download the dependencies.

- we *create the release bundle*
by running `flutter build web --release --web-renderer html`.
**This will create the bundle inside `build/dev`**.

- finally, we make use of the 
[`JamesIves/github-pages-deploy-action@v4`](https://github.com/JamesIves/github-pages-deploy-action)
action to *automatically* deploy the website to GithubPages.
It pushes and deploys the code found in `./build/web` 
(the generated release bundle)
from the `gh-pages` branch.

And that's it!
You are using the `JamesIves/github-pages-deploy-action@v4`,
which will do all the heavy lifting for you!
You just need to tell it 
*which branch you want to serve the files from*
and *which repo folder it needs to pull the release bundle from*.

If you check the `gh-pages` branch
(it *doesn't need to be created prior to running the action,
it will create it by default if it's not found*),
you will see that the files are automatically
pushed to this branch 
and all ready to be *served* by Github Pages!

<img width="899" alt="final" src="https://user-images.githubusercontent.com/17494745/222711274-b1b0df63-a2b5-41f2-947d-cd45e7dafaa5.png">


# Star the repo!

As always, if you find it helpful/useful please star the repo on GitHub ⭐ 🙏.
If you get stuck or have any questions/suggestions,
please [open an issue](https://github.com/dwyl/learn-github-pages/issues).