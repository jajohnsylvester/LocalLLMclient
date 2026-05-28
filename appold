import requests
import streamlit as st

# Configure the page
st.set_page_config(page_title="LLM Chat", page_icon="🤖", layout="centered")
st.title("🤖 My Remote LLM Chatbot")

# Define the ngrok base URL (passed via Environment Variables for security)
# Defaulting to your current URL if not found in environment variables
NGROK_URL = st.sidebar.text_input(
    "API Endpoint URL",
    value="https://evident-lens-surpass.ngrok-free.dev",
)

# You can change this to match the model you have pulled in Ollama (e.g., llama3, mistral)
MODEL_NAME = st.sidebar.text_input("Model Name", value="llama3")

# Initialize chat history in session state if it doesn't exist
if "messages" not in st.session_state:
    st.session_state.messages = []

# Display prior chat history
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])


# Function to communicate with the remote Ollama server via ngrok
def query_llm(prompt, history):
    # Constructing standard Ollama generation payload
    # If using standard OpenAI compatible formats change endpoint to /v1/chat/completions
    url = f"{NGROK_URL.rstrip('/')}/api/generate"

    headers = {
        "Content-Type": "application/json",
        "ngrok-skip-browser-warning": "true",  # Bypasses ngrok interstitial
    }

    payload = {
        "model": MODEL_NAME,
        "prompt": prompt,
        "stream": False,  # Set to True if you want to implement streaming logic later
    }

    try:
        response = requests.post(url, json=payload, headers=headers, timeout=30)
        if response.status_code == 200:
            return response.json().get("response", "No response text found.")
        else:
            return f"Error {response.status_code}: {response.text}"
    except Exception as e:
        return f"Failed to connect to the LLM backend: {e}"


# Accept user input
if user_input := st.chat_input("Ask something..."):
    # Display user message
    with st.chat_message("user"):
        st.markdown(user_input)
    st.session_state.messages.append({"role": "user", "content": user_input})

    # Generate and display assistant response
    with st.chat_message("assistant"):
        with st.spinner("Thinking..."):
            response_text = query_llm(user_input, st.session_state.messages)
            st.markdown(response_text)

    st.session_state.messages.append(
        {"role": "assistant", "content": response_text}
    )
