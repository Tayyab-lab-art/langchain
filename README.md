import streamlit as st
import google.generativeai as genai
from openai import OpenAI
import PIL.Image
import io

# --- 1. IDENTITY & CONFIG ---
st.set_page_config(page_title="Tayyab AI Hub", layout="wide")

if "creator" not in st.session_state:
    st.session_state.creator = "Mr. Tayyab"
if "theme_color" not in st.session_state:
    st.session_state.theme_color = "#10a37f" # ChatGPT Green
if "bg_color" not in st.session_state:
    st.session_state.bg_color = "#ffffff"

# --- 2. CUSTOM CSS (For Admin Customization) ---
st.markdown(f"""
    <style>
    .stApp {{
        background-color: {st.session_state.bg_color};
    }}
    .chat-bubble {{
        padding: 15px;
        border-radius: 15px;
        margin-bottom: 10px;
        color: black;
    }}
    </style>
    """, unsafe_allow_index=True)

# --- 3. SIDEBAR (Navigation & Model Selection) ---
with st.sidebar:
    st.title("🤖 Tayyab AI Hub")
    
    # User Recognition
    user_name = st.text_input("Enter your name:", placeholder="Who are you?")
    if user_name.lower() == "mr. tayyab":
        st.success("Welcome, Boss! Creator mode active.")
        is_admin = True
    else:
        is_admin = False

    st.divider()
    
    # API Model Selection
    model_choice = st.selectbox("Select AI Model", 
        ["Gemini 2.0 Pro", "Gemini Flash", "ChatGPT-5 (Simulated)", "ChatGPT Mini", "NanoPro (Images)", "VideoGen API"])

    # API Keys Section (Users can paste here)
    with st.expander("🔑 API Settings"):
        gemini_key = st.text_input("Gemini API Key", type="password")
        openai_key = st.text_input("OpenAI API Key", type="password")

# --- 4. ADMIN PANEL (Hidden for Others) ---
if is_admin:
    with st.expander("🛠️ ADMIN CONTROL PANEL"):
        st.subheader("Global Customization")
        st.session_state.theme_color = st.color_picker("Change Theme Color", st.session_state.theme_color)
        st.session_state.bg_color = st.color_picker("Change Background Color", st.session_state.bg_color)
        font_size = st.slider("Font Size", 12, 30, 16)
        st.write("Database Status: Connected to Supabase (Simulated)")

# --- 5. MAIN CHAT INTERFACE ---
st.header(f"Chat with {model_choice}")

# File Uploaders (Multimodal)
uploaded_files = st.file_uploader("Upload Videos, Docs, or Images", accept_multiple_files=True)

if "messages" not in st.session_state:
    st.session_state.messages = []

# Display Chat History
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# Chat Input
if prompt := st.chat_input("Ask Mr. Tayyab's AI anything..."):
    st.session_state.messages.append({"role": "user", "content": prompt})
    with st.chat_message("user"):
        st.markdown(prompt)

    # Logic for API Call (Example: Gemini)
    with st.chat_message("assistant"):
        if "Gemini" in model_choice:
            if not gemini_key:
                st.error("Please add Gemini API key in Sidebar!")
            else:
                try:
                    genai.configure(api_key=gemini_key)
                    model = genai.GenerativeModel('gemini-pro')
                    response = model.generate_content(prompt)
                    st.write(response.text)
                    st.session_state.messages.append({"role": "assistant", "content": response.text})
                except Exception as e:
                    st.error(f"Error: {e}")
        
        elif "NanoPro" in model_choice:
            st.write("Generating high-quality image... (Simulated)")
            # Simulating an image download button
            st.button("📥 Download Image")
            
        else:
            st.write(f"Connecting to {model_choice} backend...")

# --- 6. FOOTER ---
st.divider()
st.caption("Powered by Tayyab AI Hub | SaaS Ready")
