# Make.com-Automated-Etsy-Commission-Art-Shop
This is a large automation flow that was built to free up my time as the shop owner, after failing to find a suitible manager. It is the culmination of over a year of experience refining smaller past flows into the current all-encompassing build. Unlike other flows featured on my Github, this is one that is actively used as of the time of writing.

### 🤖 External API Integrations Used:
- Google Suite (Sheets, Drive, Forms, Gmail, Tasks)
- ChatGPT
- Clickup
- Mallabe
- Imgbb
- Etsy
- Shopify (bonus pipeline)
- Printify (bonus pipeline)
- Smart Crop (bonus pipeline)

### ✨ Features:
- Does almost everything you need to run a commission art store, including automating the coordination between the clients and the artist. (The only real limitation is that it doesn't automate Etsy Messages. However, after the sale the process is handled automatically through email communication.
- Performs ETL, synchronizing all of our platforms.
- Handles every aspect of the project creation.
- Allows artists to "claim" pieces according to their personal preference, removing the need of manually assinging work.
- Implements an iterative approach where the client can request feedback and receive multiple versions of each piece.
- While it's built specifically for a commission art store, many aspects could be adapted to any service based businesses. 

### 🌊 The Flow:
1. **New Order Creation.json**
2. **Requirements Form Answered.json**
3. **Image Submission.json**
4. **Feedback.json**
5. **Product Creator.json (bonus)**

  
# Pipeline Breakdowns:
_Please note that in interest of space, the pipeline images have been compressed to fit a single screen_

Additionally, please note that there are many smaller "helper" automations within Make.com, and the Clickup platform that help control the process flow, and the management of information. The automations featured do the heavy lifting, and the most novel of the helper automations will be featured throughout. If I was to rebuild this automation today, I would also likely take slightly different approaches - this is by no means the completely optimal way to implement this automation. 


### 1. (New Order Creation.json) 
![New Order Creation](https://github.com/user-attachments/assets/34787bf8-c4f7-4cf2-9969-ccb2ff00a8c2)

This workflow runs when we have Etsy orders that have not been input into our database yet. When orders come in, a seperate automation runs to record the information on our spreadsheet, which is then grabbed by this automation. It's a pretty linear automation and runs as such:
1. The order is checked against the Etsy system to grab all the necessary data.
2. We check if the customer is a return client. If so, we also grab the name of the previous artist.
3. We gather and clean different variables from Etsy, such as the revenue (converting it into dollars), the first name using ChatGPT, and all the receipt IDs (in the case that there are multiple items within the order).
4. Next we create our folders. These are used in the order requirements form so the client can upload images, and for us to use later when uploading the completed images.
5. After some more data preperation, the last step is to create our requirements form with the client information, create our Clickup Task, send a thank you email, and update our various spreadsheets.


### 2. (Requirements Form Answered.json)
![Requirements Form Answered](https://github.com/user-attachments/assets/81e5b35f-9069-40f7-b3c4-b1ae99531763)

The second step of the pipeline happens when the client fully fills out our requirements form. Based on the data in our sheet, any outstanding requirements forms are checked. If an answer is found it moves to the next steps:
1. A project name is generated by ChatGPT based on the information provided by the client.
2. We share the image folders, and post those along with the commission requirements to our Clickup Task. A short sleep is input here to ensure everything finalizes it's posting/uploading.
3. Next, we create a submission form and a drive folder where the artist can upload the first version of the art piece.
4. Based on if it's a return client or not, the Task is moved to one of two statuses. One status to give the previous artist the ability to "claim" the piece, and another that allows any artist to claim it.
5. Depending on the status, a Clickup automation runs to move the task to another list.
6. Lastly, we send an email to the client informing them that we got their requirements, and updating them on the next step.

**An additional automation to mention here is (Autoclaim.json):**
![Autoclaim](https://github.com/user-attachments/assets/d30c4af8-b93e-46f0-85f0-e3fb6e21b4fe)

- In order to faciliate the automated claiming of pieces, each artist is given a seperate list where their tasks live. This claiming automation allows artists to comment "claim" on a task which triggers this automation to run.
- The automation checks for how many active projects they have and which list it's in, and allows for different rules depending on how long it's been unclaimed. This allows me to prevent one artist taking on all the work just because they're faster, as artists with less ongoing work will have earlier access to new pieces.
- If an artist is eligible for the piece they are claiming, the task is edited which triggers an internal Clickup automation to move the task to the correct artist's list.



### 3. (Image Submission.json)
![Image Submission](https://github.com/user-attachments/assets/e1cefc3a-cf4e-4770-8292-55fb0572fb38)


- When the artist is ready to send the piece to the client, it runs through this workflow. They trigger this by uploading an image to the Google Drive folder, and submitting a form response.
- When the response is received, this workflow will run. Additionally, it has protections in place to prevent missing images, or double submitting of images.

1. The pipeline checks the image size and ensures it's not too large for future steps. If it is, it utilizes Mallabe to downscale it, twice if necessary.
2. Based on if the image is a full submission, or just the first draft, the automation will run slightly differently, primarily with naming convention of folders.
3. The image is uploaded to the completed images, so it can be submitted along with all the other versions to the customer at the end of the process.
4. Depending on which stage the piece is in (sketch, initial submission, or revision), the pipeline will converge here. This has minor differences:
    - The email format changes to keep it fresh and specific to which stage the client is in.
    - The form format changes, with minor question changes - for example: after the sketch stage it asks the client if they want a revision or if they're happy without further changes, in the sketch stage it's automatic that there will be another stage.
    - After the initial submission the Etsy order is automatically marked as complete to avoid becoming late with long commissions that have revision requests.
  
This step is essentially the mirror to step four. It goes between #3 and #4 until the client says they're satisfied when answering the form. 



### 4. (Feedback Form Response.json)
![Feedback Form Response](https://github.com/user-attachments/assets/a917efb8-5d4b-4c21-a722-913a79f1bdf1)

This is technically the last step of the flow, but as mentioned, it goes hand in hand with step three until the client is happy.

- When the feedback form is answered, this automation runs. Firstly, it creates a task for me to check if we get a low rating out of ten on the current version. Then the workflow converges depending on if the client is finished or not.
  - If they request a revision, or it's the sketch feedback, it repeats the process in step 2, creating a new submission form/folder and passes the feedback to the artist in another comment. An email is sent to the client informing them that it's been received.
  - If they are happy with the piece a copy of the final version is saved seperately to be posted on our social media, or used in our marketing.
  - Clickup is updated that it's finished, and based on if the artist has tipping set up, the completion email is sent with a custom ChatGPT intro for a bit of flare. 


### Bonus Flow: (Product Creator.json)
![Product Creator](https://github.com/user-attachments/assets/0b28adf3-7c58-466c-a2c4-0dbc7e460e83)

This automation was mostly built as a test of whether it's possible to automatically offer physical products of the clients design. It does work, but it's a very heavy automation and it likely should be refined for better results. Note that while the main version runs alongside the contract completion with step four, the included version is set up to just run from a spreadsheet. Any errors seen in the screenshot is due to this change affect titles.

1. The start of the process is preparing the image and collecting all the variables. It resizes if necessary, determines if it's a vertical, horizontal, or square aspect ratio, and utilizes smart crop to make sure it fits the standard aspect ratio.
2. Based on the aspect ratio, a few preselected product types have been chosen.
3. Based on the product types, we automatically record the different products for the actual listing creation. Due to how Printify works, I had to implement a couple tricks to make this work:
    - Firstly, we need to create a dummy listing to get the cost of each product.
    - Secondly, we had to create a seperate path (the upper right of the flow), for each possible number of products that we manage to gather. The current version of the flow can create up to 21 product types for each commission design. Basically the problem is that all product types must be included at the time of the listing creation, therefore, each seperate path has the product count manually programmed. For example, the 21 product version has all 21 variants manually programmed in from the get go.
4. The next step is to prepare the mockups, generate the listing information, publish it on Printify, and add it to a custom Shopify collection. The regular version of the pipeline then shares this with the client so they can order any of the generated products as desired.


