# Basic Question
1. Imagine you're building a website that allows users to submit photos. One of the requirements is that each photo must be reviewed by a moderator before it can be published. How would you design the logic for this process? What technologies would you use? Do you have any data structure in mind to support this based on your technology of choice to handle those data?

**Logic Flow:**
1.  **User Uploads Photo:** A user submits a photo from the website.
2.  **Store & Tag:** The photo is saved, and its status is marked as "pending review" in the database.
3.  **Moderator Dashboard:** Moderators log into a special dashboard to see all "pending review" photos.
4.  **Review & Decide:** A moderator checks the photo and either "approves" it or "rejects" it.
5.  **Publish/Hide:** If approved, the photo becomes visible to everyone. If rejected, it stays hidden (or is deleted).
6.  **Notify User:** The user who uploaded the photo gets a notification about the decision.

**Technologies I'd Use:**
* **Frontend:** Vue.js (for the website users see and the moderator dashboard).
* **Backend:** Golang (to handle the API for photo uploads, saving to the database, and changing status).
* **Database:** PostgreSQL (to store photo details and their status).
* **Storage:** AWS S3 / GCP Bucket(for securely storing the actual photo files).
* **Queue (Optional, for big sites):** Google Pub/Sub / RabbitMQ (to handle background tasks like resizing photos or sending notifications without slowing down the main website).

# Database Questions
1. Level 1 : 
```
SELECT 
    * 
FROM 
    Customers 
WHERE 
    Country = 'Germany';
```
2. Level 2 : 
```
SELECT 
    COUNT(customerID) AS customer_count, 
    Country
FROM 
    customers
GROUP BY 
    Country
HAVING 
    COUNT(customerID) > 5
ORDER BY 
    customer_count DESC;
```
3. Level 3 : 
```
SELECT
    Customers.CustomerName,
    COUNT(Orders.OrderID) AS OrderCount,
    MIN(Orders.OrderDate) AS FirstOrder,
    MAX(Orders.OrderDate) AS LastOrder
FROM
    Customers
JOIN
    Orders ON Customers.CustomerID = Orders.CustomerID
GROUP BY
    Customers.CustomerName
HAVING
    COUNT(Orders.OrderID) >= 5
ORDER BY
    Customers.CustomerName;
```

# JavaScript/TypeScript Questions
1. Level 1:
```
function titleCase(str) {
    if (typeof str !== 'string' || str.length === 0) {
        return '';
    }

    return str
        .toLowerCase()
        .split(/[\s-]+/)
        .map(word => {
            if (word.length === 0) {
                return '';
            }
            return word.charAt(0).toUpperCase() + word.slice(1);
        })
        .join(' ');
}

console.log(`"I'm a little tea pot" should return a string: ${typeof titleCase("I'm a little tea pot") === 'string'}`);
console.log(`"I'm a little tea pot" should return "I'm A Little Tea Pot": "${titleCase("I'm a little tea pot")}"`);
console.log(`"sHoRt AnD sToUt" should return "Short And Stout": "${titleCase("sHoRt AnD sToUt")}"`);
console.log(`"SHORT AND STOUT" should return "Short And Stout": "${titleCase("SHORT AND STOUT")}"`);
```
2. Level 2: 
```
function delay(ms) {
    return new Promise(resolve =>   setTimeout(resolve, ms));
}

delay(3000).then(() => alert('runs after 3 seconds'));
```
3. Level 2.5:
```
// Helper functions converted to return Promises
function fetchDataPromise(url) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (!url) {
                reject("URL is required");
            } else {
                resolve(`Data from ${url}`);
            }
        }, 1000);
    });
}

function processDataPromise(data) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (!data) {
                reject("Data is required");
            } else {
                resolve(data);
            }
        }, 1000);
    });
}

// Rewrite using Async/Await:
async function runProcessingFlow(url) {
    try {
        const data = await fetchDataPromise(url);
        console.log("Fetched Data (from async/await):", data);

        const processedData = await processDataPromise(data);
        console.log("Processed Data (from async/await):", processedData);
    } catch (error) {
        console.error("Error in processing flow (from async/await):", error);
    }
}

runProcessingFlow("https://example.com");
```

4. Level 3-4 : 