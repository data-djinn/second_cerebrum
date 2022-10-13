[[AWS]] [[MongoDB]]

```
{
	"DocumentDB": {
		"Features": [
			"MongoDB Compatible (not the same, but can interface)",
			"Fully Managed (like RDS)",
			"Storage Autoscaling (only scales *up*, not down)",
			"JSON Indexing (resource intensive if data structure is too large)"
		],
		"Use Cases": [
			"Social media profiles",
			"Object catalogues",
			"Content management systems",
			"Anything with semi-static objects needing storage is good",
			"Anything frequently updated (e.g. transactions) is bad use case"
		]
	}
}
```