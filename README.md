# mysql_sys.schema_auto_increment_columns-

SELECT 
    `information_schema`.`columns`.`TABLE_SCHEMA` AS `TABLE_SCHEMA`,
    `information_schema`.`columns`.`TABLE_NAME` AS `TABLE_NAME`,
    `information_schema`.`columns`.`COLUMN_NAME` AS `COLUMN_NAME`,
    `information_schema`.`columns`.`DATA_TYPE` AS `DATA_TYPE`,
    `information_schema`.`columns`.`COLUMN_TYPE` AS `COLUMN_TYPE`,
    (LOCATE('unsigned',
            `information_schema`.`columns`.`COLUMN_TYPE`) = 0) AS `is_signed`,
    (LOCATE('unsigned',
            `information_schema`.`columns`.`COLUMN_TYPE`) > 0) AS `is_unsigned`,
    ((CASE `information_schema`.`columns`.`DATA_TYPE`
        WHEN 'tinyint' THEN 255
        WHEN 'smallint' THEN 65535
        WHEN 'mediumint' THEN 16777215
        WHEN 'int' THEN 4294967295
        WHEN 'bigint' THEN 18446744073709551615
    END) >> IF((LOCATE('unsigned',
                `information_schema`.`columns`.`COLUMN_TYPE`) > 0),
        0,
        1)) AS `max_value`,
    `information_schema`.`tables`.`AUTO_INCREMENT` AS `AUTO_INCREMENT`,
    (`information_schema`.`tables`.`AUTO_INCREMENT` / ((CASE `information_schema`.`columns`.`DATA_TYPE`
        WHEN 'tinyint' THEN 255
        WHEN 'smallint' THEN 65535
        WHEN 'mediumint' THEN 16777215
        WHEN 'int' THEN 4294967295
        WHEN 'bigint' THEN 18446744073709551615
    END) >> IF((LOCATE('unsigned',
                `information_schema`.`columns`.`COLUMN_TYPE`) > 0),
        0,
        1))) AS `auto_increment_ratio`
FROM
    (`information_schema`.`COLUMNS`
    JOIN `information_schema`.`TABLES` ON (((`information_schema`.`columns`.`TABLE_SCHEMA` = `information_schema`.`tables`.`TABLE_SCHEMA`)
        AND (`information_schema`.`columns`.`TABLE_NAME` = `information_schema`.`tables`.`TABLE_NAME`))))
WHERE
    ((`information_schema`.`columns`.`TABLE_SCHEMA` NOT IN ('mysql' , 'sys',
        'INFORMATION_SCHEMA',
        'performance_schema'))
        AND (`information_schema`.`tables`.`TABLE_TYPE` = 'BASE TABLE')
        AND (`information_schema`.`columns`.`EXTRA` = 'auto_increment'))
ORDER BY (`information_schema`.`tables`.`AUTO_INCREMENT` / ((CASE `information_schema`.`columns`.`DATA_TYPE`
    WHEN 'tinyint' THEN 255
    WHEN 'smallint' THEN 65535
    WHEN 'mediumint' THEN 16777215
    WHEN 'int' THEN 4294967295
    WHEN 'bigint' THEN 18446744073709551615
END) >> IF((LOCATE('unsigned',
            `information_schema`.`columns`.`COLUMN_TYPE`) > 0),
    0,
    1))) DESC , ((CASE `information_schema`.`columns`.`DATA_TYPE`
    WHEN 'tinyint' THEN 255
    WHEN 'smallint' THEN 65535
    WHEN 'mediumint' THEN 16777215
    WHEN 'int' THEN 4294967295
    WHEN 'bigint' THEN 18446744073709551615
END) >> IF((LOCATE('unsigned',
            `information_schema`.`columns`.`COLUMN_TYPE`) > 0),
    0,
    1))
