---
title: 'Private: Getting started with Go Lang'
author: Iulian
type: post
date: 2016-03-26T21:24:02+00:00
draft: true
private: true
url: /2016/03/getting-started-with-go-lang/
categories:
  - Go Lang
tags:
  - Go

---
## Intro

## Who&#8217;s using Go

## Installation

Installing GO under Ubuntu can be a bit tricky as official repositories are usually behind the latest stable version.

<p style="text-align: justify;">
  But there is a tool called the Go Version Manager (<a title="gvm GitHub " href="https://github.com/moovweb/gvm" target="_blank">gvm</a>) to help install, maintain, and even switch Go versions.
</p>

<p style="text-align: justify;">
  The installation process simply involves a clone of a GitHub repo and a single line in your <em>.bashrc</em>.
</p>

<pre class="lang:sh decode:true">$ bash &lt; &lt;(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)</pre>

Then you need to source your gvm directory to your .bashrc

<pre class="lang:sh decode:true">[[ -s "$HOME/.gvm/scripts/gvm" ]] && source "$HOME/.gvm/scripts/gvm"
</pre>

I&#8217;m using zsh so I&#8217;m adding below lines into .zshrc file:

<pre class="lang:sh decode:true"># go version manager
export GVM_ROOT=$HOME/.gvm
source $GVM_ROOT/scripts/gvm-default
 
# For GO programming
export PATH="$PATH:$HOME/.gvm/bin:$HOME/.gvm/gos/go1.6/bin"
export GOPATH="$HOME/Projects/gocode"
export PATH="$PATH:$GOPATH/bin"
export GOBIN="$GOPATH/bin"
</pre>

Then update zsh config by using:

<pre class="lang:sh decode:true">$ source .zshrc</pre>

You should be able to test GVM installation with:

<pre class="lang:sh decode:true">$ gvm version
Go Version Manager v1.0.22 installed at /home/iulian/.gvm
</pre>

This command will tell you which version of GVM is installed. If it reports a version back, then you have successfully installed GVM.

We&#8217;re going to check the available versions of GO lang and install the latest one:

<pre class="lang:sh decode:true">gvm listall
$ gvm install go1.4
Downloading Go source...
Installing go1.4...
* Compiling...

$ gvm use go1.4
Now using version go1.4
</pre>

Version 1.6 installation depends on v1.4:

<pre class="lang:sh decode:true">$ gvm install go1.4
$ gvm use go1.4
$ export GOROOT_BOOTSTRAP=$GOROOT
$ gvm install go1.6</pre>

Check the version with:

<pre class="lang:sh decode:true ">$ go version
go version go1.6 linux/amd64
</pre>

My environment variables:

<pre class="lang:sh decode:true">$ go env
GOARCH="amd64"
GOBIN="/home/iulian/Projects/gocode/bin"
GOEXE=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOOS="linux"
GOPATH="/home/iulian/Projects/gocode"
GORACE=""
GOROOT="/home/iulian/.gvm/gos/go1.6"
GOTOOLDIR="/home/iulian/.gvm/gos/go1.6/pkg/tool/linux_amd64"
GO15VENDOREXPERIMENT="1"
CC="gcc"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0"
CXX="g++"
CGO_ENABLED="1"</pre>

## Go Workspace

A workspace is a directory hierarchy with three directories at its root:

  * `src` contains Go source files,
  * `pkg` contains package objects, and
  * `bin` contains executable commands.

I keep my Go projects under $HOME/Projects/gocode

<pre class="lang:sh decode:true ">$ sudo mkdir gocode{,/bin,/pkg,/src}</pre>

## Run Hello World

&nbsp;

## Additional links

<a href="https://golang.org/" target="_blank">https://golang.org/</a>

##