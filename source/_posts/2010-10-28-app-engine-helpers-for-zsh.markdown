---
layout: post
title: "App Engine helpers for ZSH"
date: 2010-10-28 22:25
comments: true
categories: gae zsh
---
Here are some zsh helpers for [Google App Engine][] will save you lot of
typing in the long run. I’ve extendend the [oh-my-zsh project][] with my
[own plugin][] for Google App Engine, but you can easily just add these
to your .zshrc.

<!-- more -->

### Uploading app

To use this alias you need to define GAE\_EMAIL and GAE\_PASS in your
.zshrc. This saves you typing them in the first time.
<code>
alias gaeup="echo $GAE_PASS|$GAE_SDK/appcfg.py update . --email=$GAE_EMAIL --passin"
</code>

### Helpers for bulkloader

Using [bulkloader][] to download/restore multiple entities can be
painful with all the manual steps. This helper is for downloading raw
entity data from a live site for given Kind. Usage: <i>gaebulkdl Kind
app\_id</i>. **Note:** the \_ah/remote\_api is default for [Python SDK
1.3.8 builtins][].
{% codeblock %}
function gaebulkdl() {
    echo $GAE_PASS|$GAE_SDK/bulkloader.py --dump --kind $1 --filename=$1.bin \
     --url=http://$2.appspot.com/_ah/remote_api --email=$GAE_EMAIL --passin
}
{% endcodeblock %}

This function is for restoring downloaded data. Usage:
    gaebulkup Kind localhost.fi:8080 app_id email password

{% codeblock %}
function gaebulkup() {
    echo $5|$GAE_SDK/bulkloader.py --restore --kind=$1 --filename=$1.bin \
    --url=http://$2/_ah/remote_api --app_id=$3 --email=$4 --passin
}
{% endcodeblock %}

  [Google App Engine]: http://code.google.com/appengine/
  [oh-my-zsh project]: http://github.com/mikkolehtinen/oh-my-zsh
  [own plugin]: http://github.com/mikkolehtinen/oh-my-zsh/blob/master/plugins/gae.plugin.zsh
  [bulkloader]: http://code.google.com/appengine/docs/python/tools/uploadingdata.html
  [Python SDK 1.3.8 builtins]: http://googleappengine.blogspot.com/2010/10/new-app-engine-sdk-138-includes-new.html