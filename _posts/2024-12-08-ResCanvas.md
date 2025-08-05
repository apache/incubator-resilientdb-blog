
# About ResCanvas
## The existing problem
Drawing is an important aspect of art and free expression within a variety of domains. It has been used to express new ideas and works of art. Tools such as MS Paint allow for drawing to be achievable on the computer, with online tools extending that functionality over the cloud where users can share and collaborate on drawings and other digital works of art. For instance, both Google's Drawing and Canva's Draw application have a sharable canvas page between registered users to perform their drawings.

However, such online platforms store the drawing and user data in a centralized manner, making personal data easily trackable by their respective companies, and easily sharable to other third parties such as advertisers. Furthermore, the drawings can be censored by both private and public entities, such as governments and tracking agencies. Privacy is important, yet online collaboration is an essential part of many user's daily workflow. Thus, it is necessary to decentralize the data storage aspect of these online applications. It might seem that the closest working example of this is Reddit's pixel platform since all users can make edits on the same page. However, users' data are still stored centrally on their servers. Furthermore, the scope is limited to just putting one pixel at a time for each user on a rate limited basis.

## Overview of ResCanvas
Introducing ResCanvas, a breakthrough in web-based drawing platforms that utilizes ResilientDB to ensure that user's drawings are securely stored, allowing for multiple users to collaborate and create new works of art and express ideas freely without any limits, tracking, or censorship. The canvas drawing board is the core feature of ResCanvas, designed to allow users to perform drawings using their mouse or touchscreen interface. It is simple to use, yet it allows for infinite possibilities. **To the best of our knowledge, ResCanvas is the first ResilientDB application that combines the key breakthroughs in database decentralization brought forth by ResilientDB, with the power of expression that comes with art, bridging the gap between the arts and the sciences.**

ResCanvas is designed to seamlessly integrate drawings with the familiarity of online contribution between users using effective synchronization of each user's canvas drawing page. This allows for error-free consistency even when multiple users are drawing all at the same time. Multiple users are able to collaborate on a single canvas, and just as many things in life have strength in numbers, so too are the users on a canvas. One user could be working on one part of the art drawing while other users can finish another component of the drawing in a collaborative manner.

The key feature of ResCanvas is defined by having all drawings stored persistently within ResilientDB in a stroke by stroke manner. Each stroke is individually cached via in-memory data store using Redis serving as the frontend cache. This ensures that the end user is able to receive all the strokes from the other users regardless of the response latency of ResilientDB, greatly enhancing the performance for the end user. Furthermore, all users will be able to see each other's strokes under a decentralized context and without any use of a centralized server or system for processing requests and storing data.

## Key Features
* Multiple user concurrent drawing and viewable editing history on a per user basis
* Drawing data and edit history is synchronized efficiently and consistently across all users
* Fast, efficient loading of data from backend by leveraging the caching capabilities of the Redis frontend data storage framework
* Color and thickness selection tools to customize your drawings
* Persistent, secure storage of drawing data in ResilientDB allowing for censorship free expression
* No sharing of data to third parties, advertisers, government entities, .etc with decentralized storage
* Responsive, intuitive UI inspired by Google's Material design theme used throughout the app, without the tracking and privacy issues of Google's web applications
* Clear canvas ensures that data is erased for all users on the frontend

## Additional screenshots
<p align="center">
    <img src="/assets/images/rescanvas/app_overview.png" width="60%"/>
    <img src="/assets/images/rescanvas/canvas_user_edit_history.png" width="60%"/>
    <img src="/assets/images/rescanvas/color_selection_tool.png" width="60%"/>
    <img src="/assets/images/rescanvas/color_selection_tool_closeup_view.png" width="40%"/>
    <img src="/assets/images/rescanvas/help_screen.png" width="60%"/>
    <img src="/assets/images/rescanvas/login_screen.png" width="60%"/>
    <img src="/assets/images/rescanvas/clear_canvas.png" width="60%"/>
</p>


## Workflow diagram
<p align="center">
    <img src="/assets/images/rescanvas/workflow.png" width="60%"/>
</p>

The workflow of ResCanvas consists of retrieving an existing drawing by checking for a cached drawing from the frontend via the Redis cache, using the draw count serving as the identifier for the stroke on a stroke by stroke basis. If the frontend does not have the drawing data in Redis, then ResDB is queried through the `getCanvasData` GET endpoint. Similarly, strokes are not only cached on the local Redis frontend but also sent to ResDB via the commit HTTP request of `submitNewLine` POST endpoint. This ensures that the drawings will load quickly on the frontend, thereby enhancing the UX for the end user of the application by reducing the time required to load all the drawings onto the canvas and also when the canvas is refreshing as multiple users are making edits concurrently. The process of transferring data between the frontend and backend is done seamlessly in the background as the users are interacting with the canvas on their respective devices.


## Project Setup
### Step 1
* On the first terminal, navigate to the ResilientDB Key-Value Service directory: `cd ~/resdb/incubator-resilientdb`
* Run the KV-Service shell script: `./service/tools/kv/server_tools/start_kv_service.sh`

### Step 2
* On the second terminal, navigate to the GraphQL directory: `cd ~/resdb/incubator-resilientdb-graphql`
* Start the http server for the crow service endpoints: `bazel-bin/service/http_server/crow_service_main service/tools/config/interface/client.config service/http_server/server_config.config`

### Step 3
* On a third terminal, run the following commands to test the backend:
    * Submit a new request with timestamp `$ts` and data `$value` with the following request: `curl -X POST http://67.181.112.179:10010/submitNewLine \
    -H "Content-Type: application/json" \
    -d '{"ts":"1234", "value":"value1"}'`
    * And to get all the missing data starting from `$from` via the following request: `curl -X GET http://67.181.112.179:10010/getCanvasData \
    -H "Content-Type: application/json" \
    -d '{"from": "2"}'`

### Step 3.5 (optional, only if redis returns errors)
* To optionally clear the data cache from `redis`, run the following commands on another terminal:
    ```bash
    redis-cli
    FLUSHALL
    exit
    ```

### Step 4
* Navigate to the backend directory from the Res-Canvas project directory: `cd ./backend`
* Start the backend service for ResCanvas: `python app.py`
    * You may optionally test to see if the backend is working properly by submitting a sample drawing to ResilientDB: `curl -X POST -H "Content-Type: application/json" -d '{"ts": "2024-12-01T00:00:00Z", "value": "{\"drawingId\":\"drawing_123\",\"color\":\"#000000\",\"lineWidth\":5,\"pathData\":[{\"x\":10,\"y\":10},{\"x\":20,\"y\":20}],\"timestamp\":\"2024-12-01T00:00:00Z\"}"}' http://127.0.0.1:10010/submitNewLine
`

### Step 5
* Finally, start the ResCanvas frontend from this project's home directory: `npm start`
    * You should see the browser window open up with the ResCanvas application up and ready to use

## Future work
Despite the high robustness and usability of ResCanvas, there are still several potential improvements that we can implement in the future. One of them is operational transformation, which will allow us to efficiently manage concurrent edits by multiple users via the use of transform functions even under a decentralized context. Those transform functions will define how operations that are performed by one user can then be transformed to account for changes made by other users in a concurrent manner. This will also serve as the foundation for implementing live editing functionality in a style that is similar to that of Google Docs, which allows users to seamlessly observe each other's edits on the canvas in a live, real-time manner. This is particularly useful since the current implementation requires refreshing the canvas in order to see the latest updates from others and that clicking through each user's edit history is required to determine which user performed which drawing.

Another implementation that we will leave for future work is the undo and redo functionality. Such functionality requires extensive, intricate tracking of each user's edits to the canvas to ensure that the edits can be undone or reapplied properly even when there are concurrent drawings being performed across multiple users. We would also need to consider many use cases and edge conditions, such as the situation where one user makes edits to the canvas and another user builds upon the previous user by making additional edits to that same canvas page. In this case, the undo and redo functionality would need to take this into consideration to prevent edit conflicts and loss of data between users.

Last but not least, other minor UI improvements can also be implemented in the future to further polish ResCanvas. One of them would be to have the entire canvas more responsive in terms of being fully resizable to the browser window since scroll bars can sometimes appear depending on the browser window or screen size. This would force the user to scroll down to access the controls of the canvas which currently occur at times. Another potential improvement to enhance the UX would be to add an option for the user to upload their own custom profile photo and store that on ResilientDB in a decentralized manner as well. This would allow users to create their own custom image that defines their account in a more personalized manner.

## Team members
* Henry Chou - Team Leader and Full Stack Developer
* Varun Ringnekar - Full Stack Developer
* Chris Ruan - Frontend Developer
* Shaokang Xie - Backend Developer
* Yubo Bai - Frontend Developer

## Project Repository
You can access the source code and files for ResCanvas here:
https://github.com/GabeBai/Res-Convas