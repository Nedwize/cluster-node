# Load Balancing NodeJS APIs with Clustering

> Understand how we can optimize NodeJS APIs with the cluster module

- Creating a basic server with Express

```javascript
const express = require('express');
const PORT = 3000;

const app = express();
console.log(`Worker ${process.pid} started`);
app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(PORT, () => {
  console.log(`App listening on PORT ${PORT}`);
});
```

- Now let us build a basic API that performs a CPU intensive task.

```javascript
app.get('/api', function (req, res) {
  console.time('slowApi');
  const baseNumber = 7;
  let result = 0;
  for (let i = Math.pow(baseNumber, 7); i >= 0; i--) {
    result += i;
  }
  console.timeEnd('slowApi');

  console.log(`Result number is ${result} - on process ${process.pid}`);
  res.send(`Result number is ${result}`);
});
```

For a test duration of 20 seconds, with 150 requests per second and a concurrency of 10, these were the results

| Target                    | API Without Clustering |
| ------------------------- | ---------------------- |
| Total Test Time           | 20 s                   |
| Requests Per Second (RPS) | 150                    |
| Concurrency Level         | 10                     |
| Completed requests        | 2717                   |
| **Total Errors**          | **629**                |
| _Mean Latency_            | _1342.7 ms_            |
| Longest Request           | 2336 ms                |

| Target                    | API With Clustering |
| ------------------------- | ------------------- |
| Total Test Time           | 20 s                |
| Requests Per Second (RPS) | 150                 |
| Concurrency Level         | 10                  |
| Completed requests        | 2931                |
| **Total Errors**          | **0**               |
| _Mean Latency_            | _32.4 ms_           |
| Longest Request           | 239 ms              |
