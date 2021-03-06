# GET READY!

## python-frontmatter

* https://www.python.org/downloads/

### python packetmanager: pip
	
	sudo easy_install pip 

### modul: frontmatter:
	pip install python-frontmatter

* or htps://pypi.python.org/pypi/python-frontmatter/0.1.1

## bower & grunt

### node js

* http://nodejs.org/download/

### bower:
	
	sudo npm install -g bower

### grunt
	
	sudo npm install -g grunt-cli

## Sass
Needed, if you want to modify and compile sass files

### ruby

* https://www.ruby-lang.org/de/downloads/

### ruby packetmanager: rubygems

* https://rubygems.org/pages/download

### Sass

	sudo gem install sass


# GET STEADY!

## node package dependencies

	npm install
	
## javascript dependencies

	bower install

# CONFIGURE..

## "backend"
* see config.json
* for local configuration use config.local.default.json and copy it to config.local.json

## angular app

* see public_html/js/config.js

# RUN!

## watch for all chnages (content, image and sass)

	grunt observe

## watch for content and image changes only

	grunt observe-contents
	
Can be used if sass is not need or installed.

## start webserver

* for example using python:
	
		cd public_html/
		python -m SimpleHTTPServer (or any other webserver)

# CHANGE CONTENT

Contents are taken from "contentSourceDirectory" defined in config, converted from markdown to json format and copied to "contentDestinationDirectory". For files having a matching folder (with the same name in the same parent folder) the folder contents are listed in the subpages attribute.

## content repository
The content repository for kazoosh website is located here: git@github.com:barnholdy/kazoosh-website-content.git. Check it out and set "contentSourceDirectory" and "imagesSourceDirectory" in config.js.


## markdown file structure

Markdown files consit of two parts:

* YAML front matter
* markdown body


### YAML front matter
	---
	template: root/projekte/alice.html

	title: Alice im Wunderland
	teaser: Am 16.-26. November ...

	images:
	- projekte/alice_im_wunderland_1.png
	- projekte/alice_im_wunderland_2.png

	...
	
	---
	

### markdown body

	# Headline 1
	
	## Headline 2
	
	* Listitem
	
	...

### images

...

### page order in navgation

* use order attribute (CONF.nav_order_attribute) to define order in navigation
* pages with numbers smaller than zero are excluded from navigation


# CHANGE TEMPLATES

Templates are located in "public_html/templates" and are choosen using the following fallback mechanism:

1. Template path from YAML front matter
2. Template path corresponding the file path and name (e.g. for content/root/mitglieder.md it's public_html/templates/root/mitglieder.html) 
3. Template path using "default.html" as name and file path as path (iteratively ascending the folder hierarchy) (e.g. for content/root/projekte/heat.md it's public_html/templates/root/projekte/default.html and then public_html/templates/root/default.html).


# SERVER SETUP

## Setup service to watch for content changes

* create file: /etc/init/kazoosh-website-content.conf

		#script located at /etc/init/kazoosh-website-content.conf

		description "Upstart script for kazoosh website content observer."

		start on (local-filesystems and net-device-up IFACE=eth0)
		stop on shutdown

		respawn

		chdir /var/www/kazoosh.com
		exec grunt observe-contents
		
* start service
	
		sudo service kazoosh-website-content start
		
* check service status
		
		sudo initctl status kazoosh-website-content
		
* read log

		sudo cat /var/log/upstart/kazoosh-website-content.log
		
## automate deployment

* install github webhook-deployer (https://github.com/Camme/webhook-deployer)

		sudo npm install -g webhook-deployer
		sudo npm install -g nunt
		
* create config: deploys.json

		{
			"port": 8080,
			"username": "supercooluser",
			"password": "with-a-LONG-password",
			"deploys": [{
				"name": "Git Webhook pull",
				"type": "github",
				"repo": "https://github.com/barnholdy/kazoosh-website.git",
				"basepath": "/var/www/kazoosh.com",
				"command": "git pull",
				"branch": "master"
			},
			{
				"name": "Git Webhook npn",
				"type": "github",
				"repo": "https://github.com/barnholdy/kazoosh-website.git",
				"basepath": "/var/www/kazoosh.com",
				"command": "npn install",
				"branch": "master"				
			},
			{
				"name": "Git Webhook bower",
				"type": "github",
				"repo": "https://github.com/barnholdy/kazoosh-website.git",
				"basepath": "/var/www/kazoosh.com",
				"command": "bower install",
				"branch": "master"
			}]
		}
		
* add webhook to github

		Payload URL: http://<server>:<port>/incoming/webhook-deployer
		Content type: application/x-www-form-urlencoded