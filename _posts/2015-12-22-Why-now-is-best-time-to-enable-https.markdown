---
layout: post
title: 'Why now is the best time to enable HTTPS'
date: 2015-12-22 14:05:21 -0700
tags: encryption lets-encrypt ssl https
color: rgb(255,90,90)
cover: '../assets/letsencrypt-logo-horizontal.svg'
subtitle: 'The case for encrypting your website'
---
![Lets Encrypt Logo](/assets/letsencrypt-logo-horizontal.svg)

So a few days ago, a site has opened its doors to the public to test it’s beta software. Backed by many well known companies including EFF, the non-profit site seeks to help get everyone to enable https on their site. Something that should have happened a long time ago.

[LetsEncrypt.org][le] is that awesome website.

# The Why

Why is it such a big deal that a site is trying to get everyone who hosts a website to enable https? Privacy.

In light of Snowden breaking the lid open on mass collection of information, people are worried about NSA and other government agencies are tracking our habits for no reason other than to keep records; even law abiding citizens without any ties to any radical groups.

So to protect the privacy of everyone, Let's Encrypt created software to generate ssl certs for your site to use. While this has already been done, they took it a step forward and made the process automated, which all others have failed to achieve. This means that once properly setup, your site will always be encrypted with a valid SSL cert.

Currently, since it’s in open beta, there are lots of questions and issues including limited support by Hosting providers to just a hand full of companies. I currently have an active list: [Web Hosting Supporting Let's Encrypt List][web-hosting-list]

I have a SSL cert from them and it was relatively easy to setup on my Mac using this tutorial. There are plenty of tutorials on how to set it up, but I would suggest to wait for the full release if you aren’t comfortable running commands in terminal and uploading the generated certs to your hosting.

A cPanel plugin is in the works and if you wanted to help speed up the process of getting this plugin out, please vote it up: [cPanel Let's Encrypt Plugin][cpanel-lets-encrypt-plugin]

[le]: https://letsencrypt.org/
[cpanel-lets-encrypt-plugin]: https://features.cpanel.net/topic/provide-support-for-lets-encrypt-automated-certificate-management-ssl
[web-hosting-list]: https://community.letsencrypt.org/t/web-hosting-who-support-lets-encrypt/
