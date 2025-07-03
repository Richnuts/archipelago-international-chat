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
* github : https://github.com/Richnuts/archipelago-international-chat
* website : https://richnuts.github.io/archipelago-international-chat/

# Vue.js
1. Explain Vue.js reactivity and common issues when tracking changes.
2. Describe data flow between components in a Vue.js app
3. List the most common cause of memory leaks in Vue.js apps and how they can be solved.
4. What have you used for state management
5. What’s the difference between pre-rendering and server side rendering?
Answer : 
* Pre-rendering : HTML is generated at build time
* SSR : HTML is generated on each request.
# Website Security Best Practises
* Tell me all the security best practices you can think of - start with the most important ones first.
Answer : 
1. Always validate & sanitize user input.
2. Don’t expose sensitive data to FE.
3. Use proper authentication & authorization.
4. Always use HTTPS.
5. Rate limiting & CAPTCHA to prevent brute force.



# Website Performance Best Practises
* Tell me all the performance best practices you can think of - start with the most important ones first.
Answers : 
1. Caching.
2. Optimize image size.
3. Lazy load.
4. Use efficient queries & indexing on backend.

# Golang (if interviewing for a Golang job) / .NET Candidate 
Answer : 
```
// You can edit this code!
// Click here and start typing.
package main

import (
	"fmt"
	"regexp"
	"strings"
)

func main() {
	text := "Four, One two two three Three three four  four   four"

	// replace all non-word & non-space characters with space
	re := regexp.MustCompile(`[^\w\s]`)
	text = re.ReplaceAllString(text, " ")

	// lower & split
	words := strings.Fields(strings.ToLower(text))

	wordFreq := map[string]int{}
	for _, w := range words {
		wordFreq[w]++
	}

	for w, c := range wordFreq {
		fmt.Printf("%s => %d\n", w, c)
	}
}

```


# Tools (Rate yourself 1 to 5)
Git - 5
Redis - 4
VSCode / JetBrains? - 5
Linux? - 4
AWS - 4
EC2 - 4
Lambda - 3
RDS - 3 
Cloudwatch - 3
S3 - 4
Unit testing - 5 (Golang)
Kanban boards - 4

