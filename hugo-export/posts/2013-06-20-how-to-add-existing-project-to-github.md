---
title: How to add existing project to GitHub
author: Iulian
type: post
date: 2013-06-20T08:18:08+00:00
url: /2013/06/how-to-add-existing-project-to-github/
categories:
  - Setup environment
tags:
  - github

---
<p style="text-align: justify;">
  Here is how you add an existing project to Github without cloning it first. One quick option is to create the repository on Github, clone it locally and then copy all the files across.
</p>

Go to your project

<pre class="lang:sh decode:true ">$ cd my_project</pre>

Initialize the repository:

<pre class="lang:sh decode:true ">$ git init</pre>

<p style="text-align: justify;">
  You should see the following message:
</p>

<pre class="lang:sh decode:true ">Initialized empty Git repository in /path/to/my_project/.git/</pre>

<p style="text-align: justify;">
  Add all your files to the repo:
</p>

<pre class="lang:sh decode:true ">$ git add .</pre>

<p style="text-align: justify;">
  Check to see that there are changes to be committed:
</p>

<pre class="lang:sh decode:true">$ git status</pre>

<p style="text-align: justify;">
  You should see something like this:
</p>

<pre class="lang:sh decode:true "># On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached &lt;file&gt;..." to unstage)
#
#   new file:   my_project.info
#   new file:   my_project.module
#</pre>

<p style="text-align: justify;">
  In this case, my_project.info and my_project.module are the files I have in my project.
</p>

<p style="text-align: justify;">
  Commit the files.
</p>

<pre class="lang:sh decode:true ">$ git commit -m "First commit"</pre>

<p style="text-align: justify;">
  Go to Github and create a new project repository and get the repo url: <a href="https://github.com/itabara/hello-world.git" target="_blank">https://github.com/itabara/hello-world.git</a><br /> <code> </code>
</p>

## Add Github as remote origin

<p style="text-align: justify;">
  Now we need to push our changes to Github.
</p>

<p style="text-align: justify;">
  Add this as the remote origin:
</p>

<pre class="lang:sh decode:true ">$ git remote add origin https://github.com/itabara/hello-world.git
$ git remote -v
# Verifies the new remote URL</pre>

Pull from Github to local:

<pre class="lang:sh decode:true">$ git pull origin master</pre>

And finally, push the code to Github:

<pre class="lang:sh decode:true">$ git push origin master
# Pushes the changes in your local repository up to the remote repository you specified as the origin</pre>

Additional references:

  * <a href="https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/" target="_blank">https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/</a>