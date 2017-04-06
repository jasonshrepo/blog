---
layout: post
title: "Cron Job with Jekyll"
date: 2017-04-06
tag: cron
---
<br>
Cron Job With Jekyll
===
<br><br>Yesterday I tried to make Jekyll publish the running result of my lotto script automatically on default time. However I was not able to accomplish that because when I put the 'jekyll build' in my script, error message jumped up:
<br>
```ruby
def jekyll_build(path)
  Dir.chdir(path)
 %x(jekyll build)
end
```
My first error message
```sh
No such file or directory - jekyll (Errno::ENOENT)
```
then I modified the script<br>
```ruby
  def jekyll_build(path)
    Dir.chdir(File.join(File.dirname(path)))
    %x('#{Dir.pwd}/jekyll build')
  end
```
then I got a new error message<br>
```sh
sh: /my/blog/path/jekyll build: No such file or directory
```
So I thought maybe I should locate where the jekyll is, so I modified my script again
```ruby
  def jekyll_build
    %x(/home/user/.rvm/gems/ruby-2.3.3/wrappers/jekyll build -s /opt/project/blog -d /opt/project/blog/blog)
  end
```
Then I have the following message:
```sh
sh: /home/jason/.rvm/gems/ruby-2.3.3/wrappers/jekyll build -s /opt/project/blog -d /opt/project/blog/blog: No such file or directory
```

so I give it up, I turned to write a bash script and made it like this:

```sh
#!/bin/sh

/home/jason/.rvm/gems/ruby-2.3.3/wrappers/jekyll build -s /opt/project/blog -d /opt/project/blog/blog > /dev/null 2>&1
```
I use '/dev/null 2>&1' because 'jekyll build' will give you some message which will cause cron to put those into the following path: /var/spool/mail/yourusername.

so finally, I moveded the 'jekyll build' to a seperated script and made the following cron job:
```sh
0 20 * * 1,3,5 /home/jason/.rvm/rubies/ruby-2.3.3/bin/ruby /opt/project/AULOTTO/nsw-lotto.rb -g 1 -t 3 && /bin/sh /opt/project/AULOTTO/jekyllbuild.sh
0 20 * * 2 /home/jason/.rvm/rubies/ruby-2.3.3/bin/ruby /opt/project/AULOTTO/nsw-lotto.rb -g 1 -t 2 && /bin/sh /opt/project/AULOTTO/jekyllbuild.sh 
0 20 * * 4 /home/jason/.rvm/rubies/ruby-2.3.3/bin/ruby /opt/project/AULOTTO/nsw-lotto.rb -g 1 -t 1 && /bin/sh /opt/project/AULOTTO/jekyllbuild.sh
```

Everything is working properly now!

