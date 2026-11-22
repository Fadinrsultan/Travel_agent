
# 🌍 AI Travel Planner Agent

### Personalized Travel Planning with LLMs, RAG, LangChain Agent, and Real-Time Search

An intelligent AI-powered travel planning assistant that generates personalized travel itineraries based on user preferences, travel requirements, and real-time information.

The application combines **Large Language Models (LLMs)**, **Retrieval-Augmented Generation (RAG)**, **AI Agents**, and **external APIs** to create tailored travel recommendations.

Users can upload a traveler profile in PDF format, request a trip itinerary, explore hotel recommendations, and receive their generated travel plan by email.

## 🚀 Live Deployment

**Access the deployed application:**

🌐 **https://agents.fadiprojects.online**

## ✨ Key Features

- **AI-Powered Travel Planning:** Generates personalized travel itineraries using OpenAI language models.
- **Traveler Profile Analysis:** Extracts travel preferences and requirements from uploaded PDF documents.
- **Retrieval-Augmented Generation (RAG):** Retrieves relevant traveler information from a FAISS vector database.
- **Real-Time Hotel Search:** Uses SerpApi and Google Hotels to retrieve hotel options, pricing, and ratings.
- **Internet Search:** Retrieves current information about destinations, attractions, and transportation.
- **Intelligent Tool Selection:** Uses a LangChain agent to determine which tools are needed for each request.
- **Automated Email Delivery:** Sends generated travel plans through the Resend email API.
- **Personalized Recommendations:** Considers budget, accommodation preferences, interests, food preferences, and transportation requirements.

## 🏗️ System Architecture

The application follows an agent-based architecture integrating document retrieval, external APIs, and language models.

```text
                USER
                 |
                 v
       Upload Traveler Profile
                 |
                 v
          PDF Processing
          (PyPDFLoader)
                 |
                 v
         Text Chunking
                 |
                 v
        OpenAI Embeddings
                 |
                 v
          FAISS Vector DB
                 |
                 v
        +------------------+
        |  LangChain Agent |
        |   GPT-4o-mini    |
        +------------------+
                 |
       +---------+---------+
       |         |         |
       v         v         v
  Traveler    Hotel     Internet
  Profile     Search     Search
  Retrieval   SerpApi    SerpApi
       |         |         |
       +---------+---------+
                 |
                 v
        AI Travel Itinerary
                 |
                 v
           Resend API
                 |
                 v
          User Email
```

## 🧠 Technologies Used

| Technology | Purpose |
|------------|---------|
| Python | Core application development |
| OpenAI GPT-4o-mini | Language understanding and travel plan generation |
| LangChain | AI agent orchestration and tool integration |
| OpenAI Embeddings | Semantic text representations |
| FAISS | Vector storage and similarity search |
| PyPDFLoader | PDF document processing |
| RecursiveCharacterTextSplitter | Document chunking |
| SerpApi | Google Search and Google Hotels integration |
| Resend API | Automated email delivery |
| Tkinter | Local PDF file selection |
| Requests | HTTP API communication |

## 🔍 Retrieval-Augmented Generation (RAG)

The application incorporates a RAG pipeline to personalize its recommendations using information provided in the traveler's PDF profile.

### RAG Workflow

1. **Document Upload:** The user selects a PDF containing travel preferences.
2. **Document Loading:** PyPDFLoader extracts text from the PDF.
3. **Text Splitting:** The document is divided into smaller chunks.
4. **Embedding Generation:** OpenAI converts text chunks into vector embeddings.
5. **Vector Storage:** FAISS stores the resulting embeddings.
6. **Semantic Retrieval:** Relevant chunks are retrieved based on the travel request.
7. **Context-Aware Generation:** The agent uses retrieved profile information to generate personalized recommendations.

### RAG Configuration

```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=100
)

embeddings = OpenAIEmbeddings(
    model="text-embedding-3-small"
)

vectorstore = FAISS.from_documents(
    documents,
    embeddings
)
```

The retrieval system uses similarity search to identify the four most relevant document chunks.

```python
documents = vectorstore.similarity_search(
    question,
    k=4
)
```

## 🤖 AI Agent and Tools

The application uses LangChain's `create_agent()` to coordinate an OpenAI language model with custom tools.

### 1. Traveler Profile Search

**Tool:** `traveler_profile_search`

Searches the uploaded PDF profile using semantic similarity.

Retrieves information such as:

- Travel budget
- Accommodation preferences
- Preferred destinations and activities
- Dietary preferences
- Transportation requirements
- Special travel requirements

### 2. Hotel Search

**Tool:** `search_hotels`

Integrates SerpApi's Google Hotels search to retrieve accommodation recommendations.

Search parameters include:

- Destination
- Check-in date
- Check-out date
- Number of adults
- Maximum hotel price

Returned information may include hotel names, ratings, reviews, nightly rates, total prices, descriptions, and amenities.

### 3. Internet Search

**Tool:** `search_internet`

Uses Google Search through SerpApi to retrieve destination-specific information.

Examples include:

- Tourist attractions
- Local transportation
- Travel recommendations
- Destination information
- Practical travel considerations

### Agent Initialization

```python
tools = [
    traveler_profile_search,
    search_hotels,
    search_internet
]

agent = create_agent(
    model=llm,
    tools=tools,
    system_prompt=system_prompt
)
```

The agent dynamically selects the appropriate tools based on the user's travel request.

## 📧 Automated Email Delivery

After generating the travel itinerary, the application uses the Resend API to email the final plan to the user.

```python
def send_plan_email(email_address, plan):

    result = resend.Emails.send({
        "from": RESEND_FROM_EMAIL,
        "to": [email_address],
        "subject": "Your AI Travel Plan",
        "text": plan
    })

    return result
```

This enables users to retain their travel recommendations for later reference.

**Note:** Production email delivery requires an appropriately configured and verified sending domain in Resend.

## ⚙️ Installation and Setup

### 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd <YOUR_REPOSITORY_DIRECTORY>
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

Activate the environment.

**macOS / Linux**

```bash
source .venv/bin/activate
```

**Windows**

```bash
.venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install langchain langchain-openai \
langchain-community langchain-text-splitters \
faiss-cpu pypdf requests resend
```

Tkinter must also be available in the local Python installation.

### 4. Configure API Keys

The application requires API keys for:

- OpenAI
- SerpApi
- Resend

For security, configure them as environment variables rather than committing credentials to GitHub.

**macOS / Linux**

```bash
export OPENAI_API_KEY="your_openai_api_key"
export SERPAPI_API_KEY="your_serpapi_api_key"
export RESEND_API_KEY="your_resend_api_key"
```

The Python application should read these values using:

```python
OPENAI_API_KEY = os.environ["OPENAI_API_KEY"]
SERPAPI_API_KEY = os.environ["SERPAPI_API_KEY"]
RESEND_API_KEY = os.environ["RESEND_API_KEY"]

resend.api_key = RESEND_API_KEY
```

### 5. Run the Application

```bash
python main.py
```

Replace `main.py` with the actual Python script name if different.

## 🧳 Example Usage

### Step 1: Upload Traveler Profile

Select a PDF document containing your travel preferences.

Example profile information:

```text
Travel Budget: 1,000 EUR
Hotel Budget: 150 EUR per night
Preferred Activities: Museums, culture, food
Accommodation: Central location
Transportation: Public transportation
Food Preferences: Halal food
```

### Step 2: Enter Email Address

```text
Enter your private email address:
user@example.com
```

### Step 3: Submit a Travel Request

```text
Plan a trip to Paris from
2026-12-10 to 2026-12-13.
```

### Step 4: Generate the Travel Plan

The agent:

1. Searches the uploaded traveler profile.
2. Retrieves relevant preferences using FAISS.
3. Searches current hotel listings through SerpApi.
4. Retrieves destination information when required.
5. Generates a personalized itinerary.
6. Attempts to deliver the travel plan via email.

### Step 5: Receive the Travel Plan

The generated itinerary is displayed in the terminal and sent to the specified email address when email delivery succeeds.

## 📋 Example Travel Plan Structure

```text
Destination: Paris, France

Travel Dates:
December 10–13, 2026

1. Destination Overview
   - Highlights of the city
   - Travel considerations

2. Recommended Hotels
   - Hotel names and prices
   - Ratings and amenities
   - Compatibility with traveler preferences

3. Suggested Itinerary
   - Attractions
   - Museums
   - Cultural activities

4. Transportation
   - Airport transfers
   - Local public transport

5. Food Recommendations
   - Restaurants and dietary considerations

6. Practical Travel Advice
   - Budget considerations
   - Booking suggestions
```

This is an illustrative output structure; actual results depend on the request and available API information.

## 🔐 Security and Privacy

The project processes personal traveler profiles and uses external APIs.

Recommended security practices:

- Store API keys in environment variables or a secrets manager.
- Never commit API keys or sensitive traveler profiles to GitHub.
- Add `.env`, `.venv/`, and local profile files to `.gitignore`.
- Restrict access to uploaded personal documents.
- Avoid storing unnecessary traveler information.
- Rotate compromised API keys immediately.
- Review external API usage and associated costs.

## ⚠️ Limitations

- Hotel pricing depends on third-party API responses and may change.
- Hotel search results are not guaranteed booking availability.
- Generated itineraries should be verified before booking.
- Some travel information may require additional official-source verification.
- FAISS is stored in memory in the provided implementation.
- The Tkinter PDF upload interface is intended for local desktop execution.
- Email delivery depends on the Resend account configuration.

## 🔮 Future Improvements

Potential enhancements include:

- Web-based user interface
- Interactive travel planning dashboard
- Flight search API integration
- Interactive maps and route optimization
- Multi-destination itinerary planning
- Persistent traveler profiles
- Structured itinerary export to PDF
- Automated budget estimation
- Multilingual travel assistance
- User authentication and secure profile management

## 📚 Documentation and References

- [LangChain Documentation](https://docs.langchain.com/)
- [OpenAI API Documentation](https://platform.openai.com/docs/)
- [FAISS Documentation](https://faiss.ai/)
- [SerpApi Documentation](https://serpapi.com/search-api)
- [Google Hotels API](https://serpapi.com/google-hotels-api)
- [Resend Documentation](https://resend.com/docs)

## 👨‍💻 Author

**Fadi Sultan**

AI / Machine Learning Developer

**Live Application:** https://agents.fadiprojects.online

---

⭐ If you find this project useful, consider giving the repository a star.
