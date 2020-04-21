---
layout: post
title:  "What is tmux"
date:   2020-04-19 21:28:15 +0700
categories: [others]
---
tmux(Terminal MUltipleXer)는 원격 터미널 세션 안에서 여러 터미널 세션에 액세스할 수 있도록 하는 가상 콘솔을 소프트웨어이다. Tmux를 는 cli 환경에서 다수의 프로그램을 처리하고 유닉스 셸로부터 프로그램을 분리하는데 편리하게 사용할 수 있다.

Tmux를 사용하면 한 터미널에서 여러 프로그램을 쉽게 전환하고 분리 한 후 다른 터미널에 다시 연결할 수 있다.
연결된 세션이 끊어져도 실행중인 프로그램은 계속 실행된다.

Installing Tmux on Ubuntu and Debian
{% highlight bash %}
sudo apt install tmux
{% endhighlight %}

Installing Tmux on CentOS and Redhat
{% highlight bash %}
sudo yum install tmux
{% endhighlight %}

간단하게 `tmux` 명령어를 통하여 세션을 생성하고 attach 할 수 있다.
{% highlight bash %}
tmux
tmux new -s session_name
{% endhighlight %}

Tmux의 모든 명령에 사용되는 기본 단축키는 "ctrl + b" 이다. 
`ctrl + b` , `d` 단축키를 통하여 세션을 detach 할 수 있다.   
 
현재 사용중인 세션 목록을 확인하고 다시 attach 할 수 있다.
{% highlight bash %}
tmux ls
tmux attach-session -t 0 
tmux a -t 0
{% endhighlight %}

Kill Tmux sessions
{% highlight bash %}
tmux kill-session -t 0
{% endhighlight %}

**Example:**

* `Ctrl+b c`, Create a new window (with shell) 
* `Ctrl+b w`, Choose window from a list
* `Ctrl+b 0`, Switch to window 0 (by number )
* `Ctrl+b ,`, Rename the current window
* `Ctrl+b %`, Split current pane horizontally into two panes
* `Ctrl+b "`, Split current pane vertically into two panes 
* `Ctrl+b o`, Go to the next pane
* `Ctrl+b ;`, Toggle between the current and previous pane 
* `Ctrl+b x`, Close the current pane


**Documents:**

Tmux 설치 : [Installing][tmux-install]  
Tmux 문서 : [Getting-Started][tmux-getting-started] 


[tmux-install]: https://github.com/tmux/tmux/wiki/Installing
[tmux-getting-started]: https://github.com/tmux/tmux/wiki/Getting-Started
