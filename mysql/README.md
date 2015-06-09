# Templates
    # ---------




    # mysql-custom-dump example
    # --------------------------------
    #if you want to exclude Tables:
        make mysql-custom-dump TABLES="--ignore-table=dbName.tableName --ignore-table=dbName.tableName"

    #if you want to dump specific Tables:
        make mysql-custom-dump TABLES="tableName tableName"

    # mysql-prefix-dump example
        # --------------------------------

             make mysql-prefix-dump TABLE_PREFIX=prefix