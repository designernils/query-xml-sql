> # Still under construction

# Querying XML values using SQL

A quick overview of important XML queries using SQL.

## Basic SQL snippets for querying XML

Here is a basic XML snippets in a SQL column for comparison:

```XML
<Animals>
	<Mammal>
		<Rabbit lifespan_yrs = "2" sleep_time_hrs = "8">
			<Description>Plant-eating mammal, with long ears, long hind legs, and a short tail</Description>
		</Rabbit>
		<Cow lifespan_yrs = "18" sleep_time_hrs = "4">
			<Description>Fully grown female animal of a domesticated breed of ox, kept to produce milk.</Description>
		</Cow>
		<Dog lifespan_yrs = "10" sleep_time_hrs = "12">
			<Description>Domesticated carnivorous mammal that has a long snout, an acute sense of smell, non-retractable claw</Description>
		</Dog>
	</Mammal>
	<Reptile>
		<Snake lifespan_yrs = "20" sleep_time_hrs = "16">
			<Description>Long limbless reptile which has no eyelids, a short tail, and jaws</Description>
		</Snake>
		<Iguana lifespan_yrs = "15" sleep_time_hrs = "12">
			<Description>Large, arboreal lizard which has stout legs and a crest of spines from neck to tail</Description>
		</Iguana>
	</Reptile>
</Animals>
```

#### query overall XML
```SQL
SELECT
	[nature].query('(/*/Mammal)') AS 'Mammal'
FROM   
        [nature].[living_creatures]
```

>|       Mammal	       |
>| ------------------- |
>| ``` <Mammal><Rabbit lifespan_yrs = "2" sleep_time_hrs = "8"><Description>Plant-eating mammal, with long ears, long hind legs, and a short tail</Description></Rabbit><Cow lifespan_yrs = "18" sleep_time_hrs = "4"><Description>Fully grown female animal of a domesticated breed of ox, kept to produce milk.</Description></Cow><Dog lifespan_yrs = "10" sleep_time_hrs = "12"><Description>Domesticated carnivorous mammal that has a long snout, an acute sense of smell, non-retractable claw</Description></Dog></Mammal>```|

#### get XML values in a column
```SQL
SELECT
        [nature].value('(/*/Mammal/Rabbit)[1]', 'varchar(max)') AS 'Rabbit'
FROM 
        [nature].[living_creatures]
```

>|   Rabbit   |
>| ------------------- |
>|         Plant-eating mammal, with long ears, long hind legs, and a short tail	       |

#### find XML values
```SQL
SELECT * 
FROM 
      [nature].[living_creatures]
WHERE 
      [nature].value('(./Animals/Mammal)[1]', 'varchar(max)') LIKE 'a%'
```

#### find XML node with a certain attribute
```SQL
SELECT
        [nature].query('./Animals/Reptile[@sleep_time_hrs = "16"]') AS 'Which Reptile sleeps 16 hrs?'
FROM
        [nature].[living_creatures]
```


>|   Which Reptile sleeps 16 hrs?   |
>| ------------------- |
>|         Snake	       |


#### get the attribute value 
```SQL
SELECT 
        [nature].value('(/*/mammal/rabbit/@lifespan_yrs)[1]','varchar(max)') AS 'rabbit_lifespan' 
FROM 
        [nature].[living_creatures]
```

>|   rabbit_lifespan   |
>| ------------------- |
>|         2	       |


#### find XML node with an 'id' attribution and get the attribute value in the provided node location
```SQL
SELECT 
        [settings].query('(/*/Properties/Property[@id = "15583"])').value('(/Property/Field/@text)[1]', 'varchar(max)') as 'xmlQuery'
FROM 
	[settings].[Settings]
```

>|       xmlQuery      |
>| ------------------- |
>| This is an sentence |




## XML containing XMLNAMESPACES

Here is an example of a SQL column containing XML with NAMESPACES:

```XML
<Organization xmlns = "http://thisisafakewebaddress.com/namespaces">
	<General guid = "10394"></General>
</Organization>
```

and the other without NAMESPACES:

```XML
<Settings>
	<Properties>
		<Property id = "15583">
			<Field text = "This is a sentence"></Field>
		</Property>
	</Properties>
</Settings>
```


#### if looking XML attributes within XMLNAMESPACES
```SQL
WITH XMLNAMESPACES (DEFAULT 'http://thisisafakewebaddress.com/namespaces')
SELECT
	[settings].value('(/*/General/@guid)[1]','varchar(max)') AS 'xmlQuery'
FROM 
	[settings].[Settings]
```

>|       xmlQuery      |
>| ------------------- |
>|         10394       |

#### removing the XMLNAMESPACES clause, and use wildcard instead to query XML values from two different tables (containing NAMESPACES whereas the other do not)
```SQL
SELECT
	o.[orgSettings].value('(./*:Organization/*:General/@guid)[1]', 'varchar(max)') as 'XMLNAMESPACES',
	s.[settings].value('(./*:Settings/*:Properties/*:Property/@id)[1]','varchar(max)') as 'Simple XML'
FROM 
	[settings].[Organization] o,
	[settings].[Settings] s
```		

>| XMLNAMESPACES |   Simple XML  |
>| ------------- | ------------- |
>|     10394     |      15583    |
