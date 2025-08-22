Karpov Courses StartML Project, Python module.

https://karpov.courses/ml-start

---

# Description

It's the web service to access PostgreSQL database, using FastAPI and SQLAlchemy frameworks.

The database represents user and post info in social network (snapshot) and history of interaction between them.

Retrieving data from tables User, Posts and Feed the list of top liked posts per selected user can be obtained 
by http request (see below).

---

# SQL tables structure

## User_data

- age - User age (in profile)
- city - User city (in profile)
- country - User country (in profile)
- exp_group - Experimental group: some encrypted category
- gender - User's gender
- user_id - Unique user ID
- os - Operating system of the device used to access the social network
- source - Whether the user came to the app from organic traffic or from advertising

##  Post_text_df 

- post_id - Unique post identifier
- text - Text content of the post
- topic - Main topic

##  Feed_data 

- timestamp - The time when the view was made.
- user_id - The ID of the user who made the view.
- post_id - The ID of the viewed post.
- action - The type of action: view or like.
- target - 1 for views if a like was made almost immediately after the view, otherwise 0. The value is omitted for 
like actions.

---

# Modules overview 

Python ver. 3.12

- There is a dockerfile and .dockerignore inside the project. It's supposed for web service demonstration at 
https://startml_python_db-production.up.railway.app/, where you can try to send query and receive a response.

The **app.py** is used for web service operation. Then endpoint function generates user tower embedding, normalizes and dots it with all item embeddings. The obtained list of scores 
therefore helps to create a top of n posts. 

Activation using `uvicorn app:app --port 8000`, or with another port. 
Example of http query (GET method): 
http://startml_python_db-production.up.railway.app/post/recommendations/?limit=5

Other modules - like **database.py**, **schema.py**, **table_feed.py**, **table_post.py** and **table_user.py** are used 
for setting SQLAlchemy ORM and Pydantic data formats.

---

# Endpoints

- **/user/{id}**: find relevant user info by id={id} and return as JSON
- **/post/{id}**: find relevant post info  by id={id} and return as JSON
- **/user/{id}/feed/?limit=LIMIT**: should return all actions from the feed for the user with id = {id}, sorted by 
actuality with limit=LIMIT
- **/post/{id}/feed/?limit=LIMIT**: should return all actions from the feed for the post with id = {id}, sorted by 
actuality with limit=LIMIT
- **/post/recommendations/?limit=LIMIT**: should return the top limit of posts by number of likes. 
More formally: it counts the number of likes for each post, sorts them in descending order, and returns the first
limit=LIMIT of posts (their id, text, and topic)