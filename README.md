# -Movie-Recommender-System.
movie-recommender-system

    -App.py                        # Streamlit app script
    -movie_dict.pkl                # Pickled movie metadata (you’ll upload this)
    -similarity.pkl                # Pickled similarity matrix (you’ll upload this)
    -requirements.txt              # List of required Python packages
    -README.md                     # Project overview and instructions
    -assets/
        - example_output.png        # (Optional) Screenshots of the app

#  Movie Recommender System

This is a simple web application that recommends movies based on user input. It uses **Streamlit**, **machine learning**, and the **TMDB API** to fetch movie posters.

## 🔧 Tech Stack
- Python
- Streamlit
- Pandas
- Pickle
- TMDB API

##  Dataset
- `movie_dict.pkl`: Contains movie metadata like titles and IDs.
- `similarity.pkl`: Precomputed cosine similarity matrix between movies.

##  How It Works
1. User selects a movie from a dropdown.
2. The app finds 5 most similar movies using a similarity matrix.
3. TMDB API is used to fetch and display movie posters.
4. Results are shown in a clean, interactive UI using Streamlit.

##  Recommendation Logic
```python
def recommend(movie):
    index = movies[movies['title'] == movie].index[0]
    distances = similarity[index]
    movie_list = sorted(list(enumerate(distances)), key=lambda x: x[1], reverse=True)[1:6]
    return movie_list
```
##Code
```python
  import streamlit as st
import pickle
import pandas as pd
import requests
from PIL import Image
from io import BytesIO

def fetch_poster(movie_id):
    try:
        url = f"https://api.themoviedb.org/3/movie/{movie_id}?api_key=56d4f8e2fbd1b4d36e413df3479a1669&language=en-US"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        image_url = "https://image.tmdb.org/t/p/w500/" + data['poster_path']
        image_response = requests.get(image_url)
        image = Image.open(BytesIO(image_response.content))
        image = image.resize((180, 270))  # Resize to fit nicely
        return image
    except requests.exceptions.RequestException as e:
        print(f"Error fetching poster for movie ID {movie_id}: {e}")
        return "https://via.placeholder.com/500x750.png?text=No+Image"


movies_dict=pickle.load(open('movie_dict.pkl', 'rb'))
movies=pd.DataFrame(movies_dict)

def recommend(movie):
    movie_index=movies[movies['title']==movie].index[0]
    distances=similarity[movie_index]
    movies_list=sorted(list(enumerate(distances)),reverse=True,key=lambda x: x[1])[1:6]

    recommended_movies=[]
    recommended_movie_poster=[]
    for i in movies_list:
        movie_id=movies.iloc[i[0]].id

        #fetch poster from API
        recommended_movies.append(movies.iloc[i[0]].title)
        recommended_movie_poster.append(fetch_poster(movie_id))

    return recommended_movies,recommended_movie_poster


similarity=pickle.load(open('similarity.pkl', 'rb'))

st.title('Movie Recommender System')
option = st.selectbox(
    'Select Movie Recommender',
    movies['title'].values
)

if st.button('Recommend'):
   names,posters=recommend(option)

   col1, col2, col3,col4,col5 = st.columns(5)
   with col1:
       st.text(names[0])
       st.image(posters[0])

   with col2:
       st.text(names[1])
       st.image(posters[1])

   with col3:
       st.text(names[2])
       st.image(posters[2])

   with col4:
       st.text(names[3])
       st.image(posters[3])

   with col5:
       st.text(names[4])
       st.image(posters[4])
```

🌐 TMDB API
We use The Movie Database (TMDB) API to get the poster path using the movie ID.

⚠️ Note: You'll need a TMDB API key and internet connection for poster fetching to work.
▶️ Running the App

Install the dependencies:

pip install -r requirements.txt

##Then run the Streamlit app:

streamlit run App.py

-> requirements.txt
streamlit
pandas
requests
Pillow


🙌 Author

Jay Girase
Built with ❤️ using Python and Streamlit.




