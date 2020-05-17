---
layout: post
title:  "Devx command ref"
date:   2020-05-03
excerpt: "This is the list of commands useful to work with Devx"
---
- Table of Contents
{:toc}

## Devx testing files
- Run `devx sync` to sync the local workspace with the devx machine
- To run a test file use  
```devx pytest <path_of_test_file>```
- To run single test
```devx pytest <path_to_test_file>::<class_name>::<method_name>```
- To run the test in debug
```devx pytest -s <path_to_test_file>```
Add the command `import ipdb; ipdb.set_trace()` at the line you want to debug

## Tail devx logs
- Login to the devx using `devx attach`
- To tail all logs `journalctl -f`
- To tail logs of specific  service `journalctl -u devx-* -f`
- To view logs in less `journalctl -u devx-* -n 10000 | less`
- To see the list of devx services `systemctl list-unit-files --all | grep devx`

