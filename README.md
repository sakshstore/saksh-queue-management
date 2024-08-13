
# saksh-queue-management

A queue management package using Bull and MongoDB with additional features like retry mechanism, priority jobs, delayed jobs, job progress, job events, job cancellation, and monitoring.

## Installation

Install the package using npm:

```bash
npm install saksh-queue-management
```

## Usage

### Setting Up MongoDB Connection

First, set up the MongoDB connection string:

```javascript
const QueueManager = require('saksh-queue-management');
require('dotenv').config();

(async () => {
    await QueueManager.sakshSetMongoDBConnection(process.env.MONGODB_URL);
})();
```

### Adding a Job to the Queue

Add a job to the queue with optional settings:

```javascript
const myQueue = new QueueManager('userQueue');

myQueue.sakshAddJob({ task: 'Task 1' }, { priority: 1, delay: 5000 });
```

### Processing Jobs in the Queue

Define a callback function to process each job and handle retries:

```javascript
const processTask = async (task, updateProgress) => {
    console.log(`Processing task with data:`, task.data);
    for (let i = 0; i <= 100; i += 20) {
        updateProgress(i);
        await new Promise(resolve => setTimeout(resolve, 200)); // Simulate async work
    }
    console.log(`Task with data ${task.data} completed`);
};

myQueue.sakshProcessJobs(processTask);
```

### Handling Job Completion and Errors

Register callbacks for job completion and error events:

```javascript
myQueue.sakshOnComplete((job) => {
    console.log(`Job ${job.id} completed successfully`);
});

myQueue.sakshOnError((job, err) => {
    console.error(`Job ${job.id} failed with error:`, err);
});
```

### Retrieving Pending and Completed Tasks

Retrieve all pending and completed tasks from MongoDB:

```javascript
(async () => {
    const pendingTasks = await myQueue.sakshGetPendingTasks();
    console.log('Pending Tasks:', pendingTasks);

    const completedTasks = await myQueue.sakshGetCompletedTasks();
    console.log('Completed Tasks:', completedTasks);
})();
```

### Cancelling a Job

Cancel a job in the queue:

```javascript
(async () => {
    await myQueue.sakshCancelJob('jobId');
})();
```

### Running the Worker Script

Create a worker script (worker.js) to continuously process jobs:

```javascript
const QueueManager = require('saksh-queue-management');
require('dotenv').config();

(async () => {
    await QueueManager.sakshSetMongoDBConnection(process.env.MONGODB_URL);

    const myQueue = new QueueManager('userQueue');

    const processTask = async (task, updateProgress) => {
        console.log(`Processing task with data:`, task.data);
        for (let i = 0; i <= 100; i += 20) {
            updateProgress(i);
            await new Promise(resolve => setTimeout(resolve, 200)); // Simulate async work
        }
        console.log(`Task with data ${task.data} completed`);
    };

    myQueue.sakshProcessJobs(processTask);

    myQueue.sakshOnComplete((job) => {
        console.log(`Job ${job.id} completed successfully`);
    });

    myQueue.sakshOnError((job, err) => {
        console.error(`Job ${job.id} failed with error:`, err);
    });
})();
```

Run the worker script using Node.js:

```bash
node worker.js
```

Or use a process manager like PM2:

```bash
npm install pm2 -g
pm2 start worker.js
pm2 logs worker
```

## Features

- Retry Mechanism: Retries failed jobs a specified number of times.
- Priority Jobs: Allows adding jobs with different priorities.
- Delayed Jobs: Supports delayed jobs that start processing after a certain delay.
- Job Progress: Tracks and updates the progress of a job.
- Job Events: Emits custom events for job lifecycle events (e.g., job added, job started, job completed).
- Job Cancellation: Adds the ability to cancel jobs that are in the queue but not yet processed.
- Dashboard for Monitoring: Integrates with bull-board to monitor and manage your queues through a web interface.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

susheelhbti susheel2339 at gmail.com

### Summary

This `README.md` file provides an overview of the package, installation instructions, usage examples, and details about the features. You can customize the author information and repository links as needed.
