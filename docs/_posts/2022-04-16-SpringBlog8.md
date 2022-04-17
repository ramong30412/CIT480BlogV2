---
layout: post
title: 'Linting'
date: '2022-04-16 '
Categories: Analysis Tools 
---


![Lint Example](https://2.bp.blogspot.com/-W-r4JhntCZs/Xodb0fKFeYI/AAAAAAAAYyE/HuCKVrhp_rQlkfHgoiQf0p1mdAUn6441ACPcBGAYYCw/s1600/terraform-ci-steps.png)

What is Linting? Linting is the act of implementing an automated code check for your files, it can span across a range of different languages. Linting does an analysis that searches for stuff like coding errors, spelling errors, bugs, stylistic errors, and even suspicious constructs. One good use is to implement Lint into our github repos, this helps us have to test code less times since itll catch a lot of common errors and dumb errors like typos. This is a part of a process called CI (Continuous Integration) which as devs saves up lots  of time and helps us produce cleaner code. 

One of the best environments to implement this is github, where we can search for Lint files that comb through terraform code for example. We can google search ‘how to set up TFLin’ and that give us a good introduction on how to set up a lint files that A) is deployed after a github event/action and B) Checks terraform files in our repo and checks for possible errors there.  The file is write and yaml and looks something like:

    name: Lint
    on:
    push:
      branches: [ master ]
    pull_request:

    jobs:
      tflint:
      runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]

    steps:
    - uses: actions/checkout@v2
      name: Checkout source code

    - uses: actions/cache@v2
      name: Cache plugin dir
      with:
        path: ~/.tflint.d/plugins
        key: ${{ matrix.os }}-tflint-${{ hashFiles('.tflint.hcl') }}

    - uses: terraform-linters/setup-tflint@v1
      name: Setup TFLint
      with:
        tflint_version: v0.29.0

    - name: Show version
      run: tflint --version

    - name: Init TFLint
      run: tflint --init

    - name: Run TFLint
      run: tflint -f compact

I think that there's a few note worthy things here. The first being under the name block of the code here we name our lint file, we also specify the branch to search files in as well as which actions will trigger this workflow in github, this coded executes it after a push and pull request in main. The second note worth part is that we can specify which version of this lint tool we want to use, and pulls it from github for us. This file can be edited to search for different OS types, and can be modified to search after every push. Its a simple but useful tools for us to use when working with terraform!
