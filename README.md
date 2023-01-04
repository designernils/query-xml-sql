# Querying XML values using SQL

A quick overview of important XML queries using SQL.

## Basic SQL snippets for querying XML

Here is a basic XML snippets in a SQL column for comparison:

```XML
<Animals>
	<Mammal>
		<Rabbit lifespan_yrs = "2" sleep_time_hrs = "8">
			<Description available = "True">Plant-eating mammal, with long ears, long hind legs, and a short tail</Description>
		</Rabbit>
		<Cow lifespan_yrs = "18" sleep_time_hrs = "4">
			<Description available = "True">Fully grown female animal of a domesticated breed of ox, kept to produce milk.</Description>
		</Cow>
		<Dog lifespan_yrs = "10" sleep_time_hrs = "12">
			<Description available = "True">Domesticated carnivorous mammal that has a long snout, an acute sense of smell, non-retractable claw</Description>
		</Dog>
	</Mammal>
	<Reptile>
		<Snake family = "Boidae" lifespan_yrs = "25" sleep_time_hrs = "16">
			<Description available = "False"></Description>
		</Snake>
		<Snake family = "Biperidae" lifespan_yrs = "15" sleep_time_hrs = "16">
		    <Description available = "True">Venomous snake that attains a length of about two feet, varies in color and is usually not fatal to humans</Description>
		</Snake>
		<Iguana lifespan_yrs = "15" sleep_time_hrs = "12">
			<Description available = "True">Large, arboreal lizard which has stout legs and a crest of spines from neck to tail</Description>
		</Iguana>
	</Reptile>
	<Fish>&lt;?xml version="1.0"?&gt;
		&lt;Shark&gt;
			&lt;Description&gt;A long-bodied chiefly marine fish with a cartilaginous skeleton&lt;/Description&gt;
		&lt;/Shark&gt;
	</Fish>
</Animals>
```

#### searching for a specific string in XML if available
```SQL
SELECT *
FROM 
	[nature].[living_creatures]
WHERE
	CAST([nature] as nvarchar(max)) LIKE '%fish%'
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
>| ``` <Mammal><Rabbit lifespan_yrs="2" sleep_time_hrs="8"><Description>Plant-eating mammal, with long ears, long hind legs, and a short tail</Description></Rabbit><Cow lifespan_yrs="18" sleep_time_hrs="4"><Description>Fully grown female animal of a domesticated breed of ox, kept to produce milk.</Description></Cow><Dog lifespan_yrs="10" sleep_time_hrs="12"><Description>Domesticated carnivorous mammal that has a long snout, an acute sense of smell, non-retractable claw</Description></Dog></Mammal>```|

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
SELECT 
      [nature] 
FROM 
      [nature].[living_creatures]
WHERE 
      [nature].value('(./Animals/Mammal)[1]', 'varchar(max)') like '%plant%''
```

>|   nature   |
>| ------------------- |
>|         ``` <Mammal><Rabbit lifespan_yrs="2" sleep_time_hrs="8"><Description>Plant-eating mammal... ```	       |

#### find XML node with a certain attribute
```SQL
SELECT
        [nature].query('./Animals/Reptile/Snake[@family = "Biperidae"]') AS 'Biperidae'
FROM
        [nature].[living_creatures]
```


>|   Biperidae   |
>| ------------------- |
>| ```<Snake family="Biperidae" lifespan_yrs="15" sleep_time_hrs="16"><Description available="True">Venomous snake that attains a length of about two feet, varies in color and is usually not fatal to humans</Description></Snake>``` |


#### get the attribute value 
```SQL
SELECT 
        [nature].value('(/*/Mammal/Rabbit/@lifespan_yrs)[1]','varchar(max)') AS 'rabbit_lifespan'
FROM 
        [nature].[living_creatures]
```

>|   rabbit_lifespan   |
>| ------------------- |
>|         2	       |


#### find XML node with an 'id' attribution and get the attribute value in the provided node location
```SQL
SELECT 
        [nature].query('(./Animals/Reptile/Snake[@family = "Boidae"])').value('(/Snake/Description/@available)[1]', 'varchar(max)') as 'Description available'
FROM 
	[nature].[living_creatures]
```

>|       Description available      |
>| ------------------- |
>| False |



## Convert escaped XML to XML
```SQL
SELECT settings
	,(SELECT 
			CONVERT(xml, n.c.value('.', 'varchar(max)'))
		FROM 
			lc.settings.nodes('./Animals/Fish/text()') n(c)) as 'Shark'
FROM  
	[nature].[living_creatures] lc
```

>|       Shark      |
>| ------------------- |
>| ```<Shark><Description>A long-bodied chiefly marine fish with a cartilaginous skeleton></Description></Shark>``` |

## XML containing XMLNAMESPACES

Here is an example of a SQL column containing XML with NAMESPACES:

```XML
<Hierarchy xmlns = "http://thisisafakenatureaddress.com/namespaces">
	<General id = "16180"></General>
</Hierarchy>
```

and the other without NAMESPACES:

```XML
<Universum>
	<Properties>
		<Property id = "667300">
			<Field text = "This is the light speed"></Field>
		</Property>
	</Properties>
</Universum>
```



#### if looking XML attributes within XMLNAMESPACES
```SQL
WITH XMLNAMESPACES (DEFAULT 'http://thisisafakenatureaddress.com/namespaces')
SELECT
	[nature].value('(./Hierarchy/General/@id)[1]','varchar(max)') AS 'General ID'
FROM 
	[nature].[Hierarchy]
```

>|       General ID      |
>| ------------------- |
>|         16180       |

#### removing the XMLNAMESPACES clause, and use wildcard instead to query XML values from two different tables (containing NAMESPACES whereas the other do not)
```SQL
SELECT
	h.[nature].value('(./*:Hierarchy/*:General/@id)[1]', 'varchar(max)') as 'Hierarchy General ID',
	u.[nature].value('(./*:Universum/*:Properties/*:Property/@id)[1]','varchar(max)') as 'Universum Property ID'
FROM 
	[nature].[Hierarchy] h,
	[nature].[Universum] u
```		

>| Hierarchy General ID |   Universum Property ID  |
>| -------------------- | ------------------------ |
>|     16180     |      667300    |
