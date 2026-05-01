import streamlit as st
import pandas as pd

# Page config
st.set_page_config(page_title="Calorie Counter", page_icon="🍎")

st.title("🍎 Calorie Counter App")

# Extended Food Database (calories approx per serving)
food_data = {
    # Pakistani foods
    "biryani": 290,
    "nihari": 350,
    "paey": 300,
    "qorma": 330,
    "pulao": 280,
    "fried rice": 330,
    "mayo roll": 400,

    # Fast food / unhealthy
    "pizza": 285,
    "burger": 354,
    "fried chicken": 320,
    "fries": 365,

    # Meat / protein
    "steak": 271,
    "chicken breast": 165,
    "egg": 78,

    # Fruits (healthy)
    "apple": 95,
    "banana": 105,
    "orange": 62,
    "mango": 150,
    "grapes": 104,

    # Vegetables (healthy)
    "carrot": 25,
    "cucumber": 16,
    "broccoli": 55,
    "spinach": 23,
    "tomato": 22,

    # Dry fruits
    "almonds": 164,
    "cashews": 157,
    "walnuts": 185,
    "dates": 66,
    "raisins": 85,

    # Basic items
    "bread": 80,
    "milk": 120,
    "rice": 200
}

# Session state
if "food_log" not in st.session_state:
    st.session_state.food_log = []

# Clear button
if st.button("🗑 Clear All"):
    st.session_state.food_log = []
    st.rerun()

st.subheader("Add Your Meal")

# Input
food_input = st.text_input("Enter Food Name (e.g. biryani, apple, pizza)").lower()
quantity = st.number_input("Quantity", min_value=1, value=1)

# Add food
if st.button("➕ Add Food"):

    if food_input in food_data:
        calories = food_data[food_input] * quantity

        st.session_state.food_log.append({
            "Food": food_input.title(),
            "Quantity": quantity,
            "Calories": calories
        })

        st.success(f"{food_input.title()} added!")

    else:
        st.error("❌ Food not in menu! Please enter a valid food.")

# Display data
if st.session_state.food_log:

    df = pd.DataFrame(st.session_state.food_log)

    st.subheader("📋 Daily Food Log")
    st.table(df)

    total_calories = df["Calories"].sum()
    st.subheader(f"🔥 Total Calories: {total_calories} kcal")

    goal = st.number_input("Set Daily Calorie Goal", min_value=0, value=2000)

    if goal > 0:
        progress = min(total_calories / goal, 1.0)
        st.progress(progress)

        if total_calories > goal:
            st.error("⚠️ You exceeded your calorie goal!")
        else:
            st.success("✅ You are within your goal!")
    else:
        st.warning("Please enter a goal greater than 0")

    st.subheader("📊 Calories by Food")
    chart_data = df.groupby("Food")["Calories"].sum()
    st.bar_chart(chart_data)

else:
    st.info("No food added yet. Start by adding a meal 👆")
