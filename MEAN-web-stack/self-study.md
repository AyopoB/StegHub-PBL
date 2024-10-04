

### OSI Model Notes

Just revisited the OSI model, which is such a foundational concept in networking. Here’s a quick breakdown of the seven layers:

1. **Physical Layer**: This is all about the actual hardware and transmission of raw bits over a medium, like cables and switches. It’s where the physical connections happen.
  
2. **Data Link Layer**: This layer manages error detection and correction as well as framing data packets. Think Ethernet and PPP protocols.
  
3. **Network Layer**: Responsible for routing packets of data from source to destination across networks. This is where IP and ICMP come into play.
  
4. **Transport Layer**: This layer ensures reliable transmission. TCP and UDP are key players here—TCP guarantees delivery, while UDP is faster but less reliable.
  
5. **Session Layer**: Manages sessions between applications. It helps with establishing, maintaining, and terminating connections (like API calls).
  
6. **Presentation Layer**: Acts as a translator for the data. It handles things like encryption and compression, making sure data is in a usable format.
  
7. **Application Layer**: This is where users interact with software applications. Protocols like HTTP and FTP are common here.

### Load Balancing Notes

Dove into load balancing today, and it’s fascinating how it optimizes resource use and enhances reliability! Here’s what I learned:

- **Overview**: Load balancing distributes network traffic across multiple servers, preventing any single server from becoming a bottleneck.

- **Types**:
  1. **Round Robin**: Simple but effective, it cycles through servers one by one.
  2. **Least Connections**: Smart choice! It directs traffic to the server with the fewest active connections, balancing the load effectively.
  3. **IP Hash**: Uses client IP addresses to assign requests to servers. Great for maintaining session persistence.
  4. **Weighted Load Balancing**: Allows for assigning weights to servers based on their capacity, ensuring powerful servers handle more requests.

- **Techniques**:
  - **DNS Load Balancing**: Directs traffic to different IPs based on DNS records. It’s a high-level approach but effective.
  - **Application Load Balancers**: These work at the application layer and can make routing decisions based on the content of the request (like URLs).
  - **Network Load Balancers**: Operate at the transport layer, focusing on IP addresses and port numbers. They’re great for high-performance scenarios.

### HTML + CSS + JS Web Form Editing

I spent some time practicing editing simple web forms using HTML, CSS, and JavaScript. Here’s a quick summary of my learning:

- **HTML**: The backbone of the form. I used `<form>` tags to create the structure, `<input>` tags for fields like text and email, and `<button>` for submissions.

- **CSS**: This is where I made the forms visually appealing. I learned to style elements using classes and IDs—adding margins, padding, colors, and even hover effects.

- **JavaScript**: The fun part! I added interactivity with event listeners. For instance, on form submission, I could validate inputs and provide instant feedback (like error messages) without refreshing the page. 

Overall, it was a productive session revisiting these concepts. They’re not only essential in understanding networking but also in developing web applications. Excited to keep building on this knowledge!

--- 
