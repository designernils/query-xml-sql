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
## XML containing XMLNAMESPACES

### if looking XML attributes within XMLNAMESPACES
```SQL
WITH XMLNAMESPACES (DEFAULT 'http://thisisafakewebaddress.com')
SELECT
	[settings].value('(/*/Properties/Property/@id)[1]','varchar(max)') AS 'xmlQuery'
FROM 
	[settings].[Settings]
```

### ridding of XMLNAMESPACES clause, and use wildcard instead to query XML values from two different tables (containing NAMESPACES whereas the other do not)
```SQL
SELECT
	o.settings.value('(./*:Organization/*:General/@guid)[1]', 'varchar(max)') as 'Column containing XMLNAMESPACES' ,
	s.settings.value('(./*:Settings/*:Properties/*:Property/@id)[1]','varchar(max)') as 'Column containing simple XML'
FROM 
	[settings].[Organization] o,
	[settings].[Settings] s
