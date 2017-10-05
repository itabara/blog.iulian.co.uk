---
title: What Is Your Software Development Directory Structure ?
author: Iulian
type: post
date: 2013-12-30T08:34:30+00:00
url: /2013/12/what-is-your-software-development-directory-structure/
categories:
  - Source code

---
This is a fairly subjective topic as individuals or development groups each have their own philosophy. In this topic I&#8217;ll present mine. It you find that structure useful for you, at the end of this article you will be able to download the batch file to create the folder structure for you.

**drive:** I default to the _D drive_ if I have one. Otherwise, _C drive_.

  * **archive**: This is my project junkyard, for projects I no longer maintain or use, but which I can&#8217;t quite bring myself to delete.

  * **doc**: This is a good place for general software development documentation, best practices docs, or process and procedures sorts of documents. A copy of this post will likely wind up there.
  * **images**: Contains supporting images for docs found in the doc parent folder.

  * **resources**: These are resources that span multiple products/projects, like a company logo, for example. You might need or want more than just the sub-folders I currently have defined.
  * icons

  * images

  * **sandbox**: My development sandbox. This is the ultimate in junk drawers, where anything goes. I dump sample code here, as well as &#8216;trial and error&#8217; types of projects. None of it is ever checked-in. If I do want to check something from here in, it gets moved into a solutions folders.

  * **setup**: This is an environment-wide area for any utilities, batch files, etc. I use to setup my development or build environments. The batch file (download below) that creates my initial folder structure lives here, for example.

  * **solutions**:
  * **iSCM **(this is just a generic project name placeholder; rename as needed) This is where the .sln file lives. Individual projects then exist under the branches/tags/trunk subfolders.
  * **backup**: Sometimes I like to take zip file snapshots of my source, or I might have a version of some file that I want to hang on to separate from its source control location. Think of this as a generic project backup folder.

  *  **doc**: This is for project-specific documentation, such as technical or design specs, or usage guidelines.
  *  **images**

  *  **branches**

  *  **tags**

  *  **trunk**

  *  **test**
  *  **artifacts: **So far this winds up being a repository for any files my integration tests require. Not all projects have integration tests, so not all project use this folder.

  *  **tools**: This is where, broken down by sub-folder, every tool, gadget, utility, add-on, etc. that I use either for development or as part of my build environment, goes. This might include <a href="http://confluence.public.thoughtworks.org/display/CCNET/Welcome+to+CruiseControl.NET" title="CruiseControl.NET" target="_blank">CruiseControl.NET</a>, <a href="http://www.nunit.org/index.php" title="NUnit" target="_blank">NUnit</a>, <a href="http://www.jetbrains.com/resharper/" title="NUnit" target="_blank">ReSharper</a>, etc. Basically any piece of software that I might need to download and install in order to restore my build environment or machine.

Click here to download folder structure.