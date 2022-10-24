[[Python]]
read_json()
	- Takes a string path to JSON or JSON data directly as a string
	- Specify data types with dtype keyword argument
	- Pandas data guesses how to arrange it in a table

# Load JSON: json_data
with open("a_movie.json") as json_file:
    json_data = json.load(json_file)
    
print(json_data.keys())
# Print each key-value pair in json_data
for k in json_data.keys():
    print(k + ': ', json_data[k])



# Import package
`import requests`
#### Assign URL to variable: url
`url = 'http://www.omdbapi.com/?apikey=72bc447a&t=social+network'`
##### Package the request, send the request and catch the response: r
`r = requests.get(url)`
#### Decode the JSON data into a dictionary: json_data
json_data = r.json()
# Print each key-value pair in json_data
```
for k in json_data.keys():
    print(k + ': ', json_data[k])
```