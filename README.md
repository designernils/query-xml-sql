# Querying XML values using SQL

A quick overview of important XML queries using SQL.

### query overall XML
```SQL
SELECT
	[settings].query('(/*/Properties)') AS 'xmlQuery'
FROM   
        [settings].[Settings]
```

### get XML values in a column
```SQL
SELECT
        [settings].value('(/*/Properties/Property)[1]', 'varchar(max)') AS 'xmlQuery'
FROM 
        [settings].[Settings]
```

### find XML values
```SQL
SELECT * 
FROM 
      [settings].[Settings]
WHERE 
      [settings].value('(/*/Properties/Property)[1]', 'varchar(max)') LIKE 'a%'
```

### find XML node with a certain attribute
```SQL
SELECT
        [settings].query('/*/Properties/Property[@id = "15583"]') AS 'xmlQuery'
FROM
        [settings].[Settings]
```


### get the attribute value 
```SQL
SELECT 
        [settings].value('(/*/Properties/Property/@id)[1]','varchar(max)') AS 'xmlQuery' 
FROM 
        [settings].[Settings]
```

### find XML node with an 'id' attribution and get the attribute value in the provided node location
```SQL
SELECT 
        [settings].query('(/*/Properties/Property[@id = "15583"])').value('(/Property/Field/@text)[1]', 'varchar(max)') as 'xmlQuery'
FROM 
	[settings].[Settings]
```

### if looking XML attributes within XMLNAMESPACES
```SQL
WITH XMLNAMESPACES('{http://thisisafakewebaddress.com}' AS 'namespace')
```
