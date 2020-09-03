# Querying XML values using SQL

A quick overview of important XML queries using SQL.

### query overall XML
SELECT
	     [settings].query('(/*/Properties)') AS 'xmlQuery'
FROM   
        [settings].[Settings]

### get XML values in a column
SELECT
        [settings].value('(/*/Properties/Property)[1]', 'varchar(max)') AS 'xmlQuery'
FROM 
        [settings].[Settings]

### find XML values
SELECT * 
FROM 
      [settings].[Settings]
WHERE 
      [settings].value('(/*/Properties/Property)[1]', 'varchar(max)') like 'a%'

### find XML node with a certain attribute
SELECT
        [settings].query('/*/Properties/Property[@id = "15583"]') AS 'xmlQuery'
FROM
        [settings].[Settings]


### get the attribute value 
SELECT 
        [settings].value('(/*/Properties/Property/@id)[1]','varchar(max)') AS 'xmlQuery' 
FROM 
        [settings].[Settings]

### find XML node with an 'id' attribution and get the attribute value in the provided node location
SELECT 
        settings.query('(/*/Properties/Property[@id = "15583"])').value('(/Property/Field/@text)[1]', 'varchar(max)') as 'xmlQuery'
FROM [settings].[Settings]
      


### if looking XML attributes within XMLNAMESPACES
WITH XMLNAMESPACES('{http://thisisafakewebaddress.com}' AS 'namespace')
