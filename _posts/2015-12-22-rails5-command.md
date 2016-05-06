---
layout: post
title: Rails 5 command
---
Rails 5使用rails来替代rake执行任务。


<!--more-->
Usage: rails COMMAND [ARGS]

The most common rails commands are:
 generate    Generate new code (short-cut alias: "g")
 console     Start the Rails console (short-cut alias: "c")
 server      Start the Rails server (short-cut alias: "s")
 test        Run tests (short-cut alias: "t")
 dbconsole   Start a console for the database specified in config/database.yml
             (short-cut alias: "db")
 new         Create a new Rails application. "rails new my_app" creates a
             new application called MyApp in "./my_app"

All commands can be run with -h (or --help) for more information.

In addition to those commands, there are:
 destroy                             Undo code generated with "generate" (short-cut alias: "d")
 plugin new                          Generates skeleton for developing a Rails plugin
 runner                              Run a piece of code in the application environment (short-cut alias: "r")
 about                               List versions of all Rails frameworks and the environment
 assets:clean[keep]                  Remove old compiled assets
 assets:clobber                      Remove compiled assets
 assets:environment                  Load asset compile environment
 assets:precompile                   Compile all the assets named in config.assets.precompile
 cache_digests:dependencies          Lookup first-level dependencies for TEMPLATE (like messages/show or comments/_comment.html)
 cache_digests:nested_dependencies   Lookup nested dependencies for TEMPLATE (like messages/show or comments/_comment.html)
 db:create                           Creates the database from DATABASE_URL or config/database.yml for the current RAILS_ENV (use db:create:all to create all databases in the config)
 db:drop                             Drops the database from DATABASE_URL or config/database.yml for the current RAILS_ENV (use db:drop:all to drop all databases in the config)
 db:fixtures:load                    Loads fixtures into the current environment's database
 db:migrate                          Migrate the database (options: VERSION=x, VERBOSE=false, SCOPE=blog)
 db:migrate:status                   Display status of migrations
 db:rollback                         Rolls the schema back to the previous version (specify steps w/ STEP=n)
 db:schema:cache:clear               Clears a db/schema_cache.dump file
 db:schema:cache:dump                Creates a db/schema_cache.dump file
 db:schema:dump                      Creates a db/schema.rb file that is portable against any DB supported by Active Record
 db:schema:load                      Loads a schema.rb file into the database
 db:seed                             Loads the seed data from db/seeds.rb
 db:setup                            Creates the database, loads the schema, and initializes with the seed data (use db:reset to also drop the database first)
 db:structure:dump                   Dumps the database structure to db/structure.sql
 db:structure:load                   Recreates the databases from the structure.sql file
 db:version                          Retrieves the current schema version number
 initializers                        Print out all defined initializers in the order they are invoked by Rails
 log:clear                           Truncates all *.log files in log/ to zero bytes (specify which logs with LOGS=test,development)
 middleware                          Prints out your Rack middleware stack
 notes                               Enumerate all annotations (use notes:optimize, :fixme, :todo for focus)
 notes:custom                        Enumerate a custom annotation, specify with ANNOTATION=CUSTOM
 rails:template                      Applies the template supplied by LOCATION=(/path/to/template) or URL
 rails:update                        Update configs and some other initially generated files (or use just update:configs or update:bin)
 restart                             Restart app by touching tmp/restart.txt
 routes                              Print out all defined routes in match order, with names
 secret                              Generate a cryptographically secure secret key (this is typically used to generate a secret for cookie sessions)
 stats                               Report code statistics (KLOCs, etc) from the application or engine
 test                                Runs all tests in test folder
 test:db                             Run tests quickly, but also reset db
 time:zones:all                      Displays all time zones, also available: time:zones:us, time:zones:local -- filter with OFFSET parameter, e.g., OFFSET=-6
 tmp:clear                           Clear cache and socket files from tmp/ (narrow w/ tmp:cache:clear, tmp:sockets:clear)
 tmp:create                          Creates tmp directories for cache, sockets, and pids
