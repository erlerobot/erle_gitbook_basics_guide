# Installing Codiad in your BBB


#####Prerequisites

In order to install Codiad, you need some software in your board: apache2, php5, git, ... You can install
this software executing this command in the shell:

 `sudo apt-get update `
 `sudo apt-get install apache2 php5 libapache2-mod-php5 php5-mcrypt git `
	
###Installation

Once, you have installed all the necessary software, change `apache2` `dir.conf`file:

` sudo vim /etc/apache2/mods-enabled/dir.conf`

and make the next change, mode index.php to the front:

(Press `shift` `I` to enter in the edit mode, make the changes, press `ESC` button and then write `:wq` to safe
and exit from vim)

`<IfModule mod_dir.c>`

`         DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm `

`</IfModule> `

Now, we have to configure Apache Virtual Hosts. To do so, move to:

`cd /etc/apache2/sistes-available `

and open `default` file:

`vim default`

Add ` Alias ... </Directory> ` lines:

`	. . . `

`    <Directory /var/www/> `

`            Options Indexes FollowSymLinks MultiViews `

`            AllowOverride None `

`            Order allow,deny `

`            allow from all `

`    </Directory> `

`    Alias /codiad /home/demouser/Codiad `

`    <Directory "/home/demouser/Codiad"> `

`            Options -Indexes FollowSymLinks MultiViews `

`            AllowOverride All `

`            Order allow,deny `

`            allow from all `

`    </Directory> `

`    ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/`

`    . . . `

restart apache2:

`sudo service apache2 restart `

Now, let's clone Codiad. Firstly, go to `/var/www`using `cd` command in your board. Once you are there:

`git clone https://github.com/Codiad/Codiad.git `
`cd Codiad `

and copy the example configuration file:

` cp config.example.php config.php `

Give the needed permissions to the folders that are going to be used:

`sudo chown -R www-data config.php workspace/ plugins/ themes/ data/ `

restart again apache, 

`sudo service apache2 restart `

and open your favourite browser, type the next ip: `192.16.7.2:8080/Codiad/` ,
follow the instalaion process!

If you would like to add [plugins] (http://market.codiad.com/) , download, unzip them and place it in Codiad/plugins.

You can [set] (https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-codiad-a-web-based-ide-on-an-ubuntu-vps)
password protection, putting another layer. (Password Protect the IDE Interface section)


##### Generating SSH Key to use GIT

If you want to do igt push/pull, you will need to generate a ssh key and to link it to your Git account.
To do so, follow the next steps (in the terminal):

1.- Generate a new key within `/var/www/.ssh ` directory :

`ssh-keygen -t rsa -C "your_email@example.com" `

Next, you'll be asked to enter a passphrase. 

2.- Change the ownership of this key to `www-data` (the group of apache2):

` chown www-data:www-data id_rsa `
` chown www-data:www-data id_rsa.pub `

3.- Then add your new key to the ssh-agent:

`eval "$(ssh-agent -s)" `
`ssh-add var/www/.ssh/id_rsa `

4.- Add your SSH Key to GitHub: copy the content of `id_rsa.pub` and paste it in a new SSH Key in your account.

5.- Try it : ` ssh -T git@github.com `

#####Recommendations

When you clone a git repository in Codiad: First create a new project. Place within the project directory, and
`git clone ` project using https url. Then, change `.git/config ` file and replace the url with the SSH type
url. This means to make the next change the value of `url`:

From `url= https://github.com/... .git` to `url =git@github.com:... .git`

Now try to make `git pull `. If you are using `CodeGit`, make left click in the directory where you've cloned the project and click on `Open Codegit`. A new window will appear, click on `settings` and write down your username and mail direction. Now, close this window and try to make `pull`.

Issue: after rebooting BBB, ssh key reads for `/etc/`the keys again, so if you write in Codiad Terminal: `git pull `, you will see a Permission denied error. But, CodeGit is still working properly, so use CodeGit interface to make your push/pulls.



Sources:

https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-codiad-a-web-based-ide-on-an-ubuntu-vps

https://github.com/Codiad/Codiad/wiki/Installation

https://help.github.com/articles/generating-ssh-keys/
