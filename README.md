Moebooru
========
An image board.
* [Source Repository](https://github.com/moebooru/moebooru)

* This is a simple guide to installing Moebooru on Debian 12
* It's not really secure, but for a VM that's only reachable on the local network it should be enough

Requirements
------------
* Debian 12

Installation
------------
* <code>apt update && apt upgrade -y</code>
* <code>apt install byobu -y</code>
* <code>byobu</code>
* <code>apt install git nodejs imagemagick jhead ruby rubygems ruby-dev gcc make libpq-dev postgresql npm -y</code>
* <code>git clone https://github.com/moebooru/moebooru.git ~/live</code>
* <code>cd ~/live</code>
* <code>sudo gem install bundler:1.17.2</code>
* <code>nano Gemfile</code>
* Add:
* <pre>gem "bun"</pre>
* <code>sudo bundle install --path ~/live/vendor/bundle</code>
* <code>npx browserslist@latest --update-db</code>
* <code>npm audit fix</code>

Postgresql setup
------------
* <code>sudo -u postgres psql</code>
* <code>CREATE USER moebooru with SUPERUSER;</code>
* <code>ALTER USER moebooru with PASSWORD 'new_usr_pass_1';</code>
* <code>CREATE DATABASE moebooru OWNER moebooru;</code>
* <code>quit;</code>

Setup
------------
* <code>cd ~/live</code>
* <code>mkdir -p public/data/{avatars,frame,frame-preview,image,inline,jpeg,preview,sample,search}</code>
* <code>chmod 755 ~/live/public</code>
* <code>cp config/database.yml.example config/database.yml</code>
* <code>cp config/local_config.rb.example config/local_config.rb</code>
* <code>bundle exec rake secret</code>
* <code>nano config/local_config.rb</code>
* Change:
* <pre>CONFIG["app_name"] = "new_name"&#10;CONFIG["server_host"] = "ur_ip"</pre>
* Add:
* <pre>CONFIG["secret_key_base"] = "rake_secret_num_1"&#10;CONFIG[:file_hosts] = { :files => CONFIG["server_host"], :assets => CONFIG["server_host"] }</pre>
* <code>nano config/database.yml</code>
* Change:
* <pre>username: moebooru&#10;password: new_usr_pass_1</pre>
* <code>bundle exec rake db:create</code>
* <code>bundle exec rake db:reset</code>
* <code>bundle exec rake db:migrate</code>
* <code>bundle exec rake i18n:js:export</code>
* <code>bundle exec rake assets:precompile</code>

Create Systemctl
------------
* <code>nano /etc/systemd/system/moebooru.service</code>
* Add:
* <pre>[Unit]&#10;Description=Moebooru's Puma HTTP Server&#10;&#10;[Service]&#10;Type=simple&#10;User=root&#10;WorkingDirectory=/root/live&#10;ExecStart=/bin/bash -c '/root/live/bin/bundle exec puma -p 9292 -b tcp://ur_ip:80'&#10;Restart=always&#10;&#10;[Install]&#10;WantedBy=multi-user.target</pre>
* <code>systemctl enable moebooru</code>
* <code>systemctl start moebooru</code>
* <code>systemctl status moebooru</code>
