# Basic Question
#### Imagine you're building a website that allows users to submit photos. One of the requirements is that each photo must be reviewed by a moderator before it can be published. How would you design the logic for this process? What technologies would you use? Do you have any data structure in mind to support this based on your technology of choice to handle those data?

##### Logic Flow
1. **User Uploads Photo:** A user submits a photo from the website.
2. **Store & Tag:** The photo is saved, and its status is marked as `"pending review"` in the database.
3. **Moderator Dashboard:** Moderators log into a special dashboard to see all `"pending review"` photos.
4. **Review & Decide:** A moderator checks the photo and either `"approves"` it or `"rejects"` it.
5. **Publish/Hide:** If approved, the photo becomes visible to everyone. If rejected, it stays hidden (or is deleted).
6. **Notify User:** The user who uploaded the photo gets a notification about the decision.

##### Technologies
- **Frontend:** Vue.js (for the user website and moderator dashboard).
- **Backend:** Golang (to handle the API for uploads, status updates, and business logic).
- **Database:** PostgreSQL (to store photo metadata and review status).
- **Storage:** AWS S3 or Google Cloud Storage (to store actual photo files securely).
- **Queue:** Google Pub/Sub or RabbitMQ (to process background tasks like resizing photos and sending notifications).

##### Data Structure

###### SQL Table Example (PostgreSQL)
```sql
CREATE TYPE photo_status AS ENUM ('pending', 'approved', 'rejected');

CREATE TABLE photos (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    file_url TEXT NOT NULL,
    status photo_status NOT NULL DEFAULT 'pending',
    created_at TIMESTAMPTZ DEFAULT NOW(),
    reviewed_at TIMESTAMPTZ,
    reviewed_by UUID REFERENCES moderators(id),
    review_description TEXT
);
```

# Database Questions
1. Level 1 : 
```sql
SELECT 
    * 
FROM 
    Customers 
WHERE 
    Country = 'Germany';
```
2. Level 2 : 
```sql
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
```sql
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
```javascript
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
```javascript
function delay(ms) {
    return new Promise(resolve =>   setTimeout(resolve, ms));
}

delay(3000).then(() => alert('runs after 3 seconds'));
```
3. Level 2.5:
```javascript
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
* Vue has a reactivity system where the UI automatically updates when data changes. A common issue is that Vue doesn’t detect adding new properties to an object or directly replacing an array element by index.
2. Describe data flow between components in a Vue.js app
* Data flows from parent to child using props, and from child to parent using events. For unrelated components, state management or an event bus is used.
3. List the most common cause of memory leaks in Vue.js apps and how they can be solved.
* Not cleaning up event listeners, timers, or subscriptions when a component is destroyed. Use the component’s lifecycle hooks to remove them.
4. What have you used for state management
* Never use vue before
5. What’s the difference between pre-rendering and server side rendering? 
* Pre-rendering : HTML is generated at build time
* SSR : HTML is generated on each request.

# Website Security Best Practises
#### Tell me all the security best practices you can think of - start with the most important ones first.
Answer : 
1. Always validate & sanitize user input.
2. Don’t expose sensitive data to FE.
3. Use proper authentication & authorization.
4. Always use HTTPS.
5. Rate limiting & CAPTCHA to prevent brute force.
6. Use security headers.
7. Keep dependencies updated.

# Website Performance Best Practises
#### Tell me all the performance best practices you can think of - start with the most important ones first.
Answers : 
1. Caching.
2. Optimize image size.
3. Lazy load.
4. Use efficient queries & indexing on backend.
5. Use of a CDN.

# Golang (if interviewing for a Golang job) / .NET Candidate 
Answer : 
```golang
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
- Git - 5
- Redis - 4
- VSCode / JetBrains? - 5
- Linux? - 4
- AWS - 4
- EC2 - 4
- Lambda - 3
- RDS - 3 
- Cloudwatch - 3
- S3 - 4
- Unit testing - 5 (Golang)
- Kanban boards - 4
