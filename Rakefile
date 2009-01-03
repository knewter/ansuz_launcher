task :launch, [:project_name, :domain] do |t, args|
  ansuz_repository = "git://github.com/knewter/ansuz.git"
  project_dir = File.join(ansuz_launch_dir, args.project_name)

  `git clone #{ansuz_repository} #{project_dir}`
  Dir.chdir project_dir
  `cp config/database.yml.example config/database.yml`
  `sed -i 's/ansuz/#{args.project_name}/' config/database.yml`
  `rake db:create:all`
  `rake db:migrate RAILS_ENV=production`
  `rake db:migrate:plugins RAILS_ENV=production`
  `rake utils:create_admin RAILS_ENV=production`
  `mkdir public/uploads`
  `mkdir log`
  puts "Now run 'sudo rake setup_webserver[#{args.project_name},#{args.domain}]'"
end

task :setup_webserver, [:project_name, :domain] do |t, args|
  project_dir = File.join(ansuz_launch_dir, args.project_name)
  config_vhost(args.domain, project_dir, args.project_name)
  restart_apache
  puts "Visit http://#{args.domain}/admin and log in as admin/admin"
end

def config_vhost domain, project_dir, project_name
  vhost_config =<<-EOF
<VirtualHost *:80>
ServerName  #{domain}
ServerAlias  www.#{domain}
DocumentRoot #{project_dir}/public
<Directory "#{project_dir}/public">
  Options FollowSymLinks
  AllowOverride None
  Order allow,deny
  Allow from all
</Directory>
RailsBaseURI /
</VirtualHost>
  EOF
  File.open("/etc/apache2/sites-available/#{project_name}", "w") do |f|
    f.write vhost_config
  end
  `a2ensite #{project_name}`
end

def restart_apache
  `/etc/init.d/apache2 restart`
end

def ansuz_launch_dir
  "/home/jadams/ansuz_launch_dir"
end
