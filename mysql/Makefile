# doma - Makefile template for Phundament 4
# =========================================
#
# see http://phundament.com
#

.PHONY: no_targets__

APP_NAME  	?= app
YII_ENV		?= dev

WORKER_SERVICE_SUFFIX 	?= cli
BUILDER_SERVICE_SUFFIX 	?= src
PHP_SERVICE_SUFFIX 		?= fpm
WEB_SERVICE_SUFFIX 		?= nginx
COMPOSE_FILE			?= docker-compose.yml
EXCLUDE                 ?=


# Add this Makefile's targets to help
# -----------------------------------

HELP_FUN = \
		%help; \
		while(<>) { push @{$$help{$$2 // 'options'}}, [$$1, $$3] if /^([\w-]+)\s*:.*\#\#(?:@([\w-]+))?\s(.*)$$/ }; \
		print "\nusage: make [config] [target]\n\n"; \
	for (keys %help) { \
		print "$$_:\n"; \
		for (@{$$help{$$_}}) { \
			$$sep = "." x (25 - length $$_->[0]); \
			print "  $$_->[0]$$sep$$_->[1]\n"; \
		} \
		print "\n"; }

.PHONY: help
help:
	@$(MAKE) -f $(DOMA_DIR)/docker/Makefile help
	#
	# App specific targets
	#
	@perl -e '$(HELP_FUN)' $(MAKEFILE_LIST)



# Mysql targets
# ------------------------


.PHONY: mysql-show-tables
mysql-show-tables:	##@mysql shows database tables
	# --------------------------------------------
	#
	# Show database Tables ($(COMPOSE_FILE))
	#
	docker-compose -p $(COMPOSE_PROJECT_NAME) -f $(COMPOSE_FILE) \
		run --rm -e YII_ENV=$(YII_ENV) $(APP_NAME)$(WORKER_SERVICE_SUFFIX) \
		sh -c '\
			  mysqlshow -h  $${DB_PORT_3306_TCP_ADDR} -u $${DB_ENV_MYSQL_USER} --password=$${DB_ENV_MYSQL_PASSWORD} $${DB_ENV_MYSQL_DATABASE} \
		'

.PHONY: mysql-full-dump
mysql-full-dump:	##@mysql make a full db dump
	# --------------------------------------------
	#
	# make a full db dump ($(COMPOSE_FILE))
	#
	docker-compose -p $(COMPOSE_PROJECT_NAME) -f $(COMPOSE_FILE) \
		run --rm -e YII_ENV=$(YII_ENV) $(APP_NAME)$(WORKER_SERVICE_SUFFIX) \
		sh -c '\
		        mkdir -p runtime/dump && \
			    mysqldump -h  $${DB_PORT_3306_TCP_ADDR} -u $${DB_ENV_MYSQL_USER} --password=$${DB_ENV_MYSQL_PASSWORD} $${DB_ENV_MYSQL_DATABASE}>\
			    runtime/dump/full-dump-$(COMPOSE_PROJECT_NAME)$$(date +%y%m%d%H%M%S).sql\
		'

.PHONY: mysql-custom-dump $(TABLES)
mysql-custom-dump:	##@mysql makes a custom dump
	# --------------------------------------------
	#
	# Makes a custom Dump with '$(TABLES)'  ($(COMPOSE_FILE))
	#
	docker-compose -p $(COMPOSE_PROJECT_NAME) -f $(COMPOSE_FILE) \
		run --rm -e YII_ENV=$(YII_ENV) $(APP_NAME)$(WORKER_SERVICE_SUFFIX) \
		sh -c '\
		        mkdir -p runtime/dump && \
			    mysqldump -h $${DB_PORT_3306_TCP_ADDR} -u $${DB_ENV_MYSQL_USER} --password=$${DB_ENV_MYSQL_PASSWORD} $${DB_ENV_MYSQL_DATABASE} $(TABLES) >\
			    runtime/dump/custom-dump-$(COMPOSE_PROJECT_NAME)$$(date +%y%m%d%H%M%S).sql\
		'

.PHONY: mysql-prefix-dump $(PREFIX)
mysql-prefix-dump:	##@mysql makes a dump of prefixed Tables
	# --------------------------------------------
	#
	# makes a dump of all with '$(PREFIX)_' prefixed Tables ($(COMPOSE_FILE))
	#
	docker-compose -p $(COMPOSE_PROJECT_NAME) -f $(COMPOSE_FILE) \
		run --rm -e YII_ENV=$(YII_ENV) $(APP_NAME)$(WORKER_SERVICE_SUFFIX) \
		sh -c '\
		        mkdir -p runtime/dump && \
		        echo "show tables like \"$(PREFIX)\\_%\";" >> runtime/dump/prefixed_tables.sql &&\
        		mysql -h $${DB_PORT_3306_TCP_ADDR} -u $${DB_ENV_MYSQL_USER} --password=$${DB_ENV_MYSQL_PASSWORD} $${DB_ENV_MYSQL_DATABASE} \
        		< 'runtime/dump/prefixed_tables.sql' | grep -v Tables_in | xargs mysqldump -h $${DB_PORT_3306_TCP_ADDR} -u $${DB_ENV_MYSQL_USER} \
		        --password=$${DB_ENV_MYSQL_PASSWORD} $${DB_ENV_MYSQL_DATABASE}\
                > runtime/dump/$(PREFIX)-dump-$(COMPOSE_PROJECT_NAME)$$(date +%y%m%d%H%M%S).sql  &&  rm -f runtime/dump/prefixed_tables.sql\
		'
