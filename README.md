# docker-react-flask


<details><summary>1. flask Dockerfile </summary>
  
```js

FROM python:3

WORKDIR /app

ENV FLASK_APP=app.py

ENV FLASK_ENV=development

COPY ./requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD [ "python", "app.py" ]


# docker build  -t pythonapp . 
# docker run -p 80:5000 -v $(pwd):/app pythonapp
```
</details>

<details><summary>2. requirements.txt file</summary>

```js
FLASK==1.1.2  
```  
</details>

<details><summary>3. flask</summary>

```js

from flask import Flask

app = Flask(__name__)

@app.route('/api', methods=['GET'])
def index():
    return {
        "name": "mehran",
        "job": "dev"
    }


if __name__ == '__main__':
    app.run(debug=True, host="0.0.0.0")

```
</details>
<details><summary>4. react Dockerfile</summary>

```js
FROM node:14-slim 

WORKDIR /user/src/app

COPY ./package.json ./

COPY ./package-lock.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]

# docker build -t reactapp .
# docker run -p 3000:3000 reactapp   
```  
</details>

<details><summary>5. react .dockerignore</summary>

```js

# Items that don't need to be in a Docker image.
# Anything not used by the build system should go here.
# Dockerfile
.dockerignore
.gitignore
README.md

# Artifacts that will be built during image creation.
# This should contain all files created during `npm run build`.
build
node_modules
  
```
</details>

<details><summary>6. react</summary>

```js
import React, { useState, useEffect } from "react";
import DisplayData from "./Components/DisplayData";

import "./App.css";

function App() {
  const [initialState, setState] = useState([]);
  const url = "/api";

  // useEffect(() => {
  //   fetch(url)
  //     .then((response) => {
  //       if (response.status === 200) {
  //         return response.json();
  //       }
  //     })
  //     .then((data) => setState(data));
  // }, []);

  // OR
  
  const fetchDatas = async () => {
    try {
      const response = await fetch(url);
      const datas = await response.json();

      setState(datas);
    } catch (error) {
      console.log(error);
    }
  };

  useEffect(() => {
    fetchDatas();
  }, []);

  return (
    <div className="App">
      <p>react hello</p>
      <DisplayData data={initialState} />
    </div>
  );
}

export default App;

```  
</details>
<details><summary>7. proxy configuration</summary>

```js
//package.json
//{
//"name": "my-app",
//"version": "0.1.0",
  "proxy": "http://server:5000/",
```
</details>
<details><summary>8. docker-compose.yml file</summary>

```js
version: "3.9"
services:
  server:
    build: ./server
    ports:
      - "80:5000"
    volumes:
      - ./server:/app
    environment:
      FLASK_ENV: development
  web:
    build: ./client/my-app
    ports:
      - "3000:3000"
    volumes:
      - ./client/my-app:/user/src/app
    depends_on:
      - server
  
```  
</details>
<details><summary>9. docker-compose up</summary></details>
<details><summary>10. check http://localhost:3000/</summary></details>
