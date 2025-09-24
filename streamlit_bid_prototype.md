Your zip-ready folder `bid-intelligence` contains the following files:

---

**1️⃣ app.py**
```python
import streamlit as st
import sqlite3
import pandas as pd

# --- DB setup ---
conn = sqlite3.connect("bids.db")
c = conn.cursor()
c.execute("""
CREATE TABLE IF NOT EXISTS bids (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    project TEXT,
    client TEXT,
    competitor TEXT,
    amount REAL,
    outcome TEXT CHECK(outcome IN ('won','lost'))
)
""")
conn.commit()

# --- App UI ---
st.set_page_config(page_title="Bid Intelligence", layout="wide")
st.title("📊 Bid Intelligence Prototype")

# --- Add new bid ---
with st.form("new_bid"):
    st.subheader("Enter New Bid")
    project = st.text_input("Project")
    client = st.text_input("Client")
    competitor = st.text_input("Competitor")
    amount = st.number_input("Bid Amount", min_value=0.0, format="%.2f")
    outcome = st.selectbox("Outcome", ["won","lost"])
    submitted = st.form_submit_button("Save Bid")
    if submitted:
        c.execute(
            "INSERT INTO bids (project,client,competitor,amount,outcome) VALUES (?,?,?,?,?)",
            (project, client, competitor, amount, outcome)
        )
        conn.commit()
        st.success("✅ Bid saved!")

# --- Display stored bids ---
st.subheader("📂 Stored Bids")
df = pd.read_sql("SELECT * FROM bids", conn)
st.dataframe(df)

# --- Basic analytics ---
if not df.empty:
    win_rate = (df["outcome"] == "won").mean() * 100
    st.metric("Overall Win Rate", f"{win_rate:.1f}%")
```

---

**2️⃣ requirements.txt**
```
streamlit
pandas
```

---

**3️⃣ README.md**
```markdown
# Bid Intelligence Prototype

This is a minimal **Streamlit** prototype for a bid intelligence system.

### Features
- Add new bids (project, client, competitor, amount, outcome)
- View stored bids in a table
- Compute overall win rate

### Deployment
1. Push this repo to GitHub
2. Go to [Streamlit Cloud](https://share.streamlit.io)
3. Click "New app" → select this repo and `app.py`
4. Deploy 🚀

### Next Steps
- Add filtering by client or competitor
- Add basic analytics per client/service
- Upgrade database to PostgreSQL for persistence
```

---

This folder is ready to be zipped and pushed to GitHub. Once pushed, you can deploy directly on Streamlit Cloud.

