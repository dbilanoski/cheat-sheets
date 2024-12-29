# Python's Streamlit Notes

Streamlit is an open-source Python library allowing to quickly create interactive web applications for data science and machine learning project directly from Python scripts.

Where Streamlit Best Fits:
- Prototyping data-driven web apps quickly.
- Building dashboards and visualization tools.
- Sharing machine learning models and data pipelines with stakeholders.
- Internal tools for data exploration and reporting.


## Basic Usage

### Installation

```powershell
pip install streamlit
```

### Example With Basic Features


```python

import streamlit as st
import pandas as pd
import io

# 1. Set the Title and Sidebar
# - This writes title directly to the web page
st.title("My First Streamlit App")
# - Add a header to the sidebar for navigation
st.sidebar.header("Navigation")

# 2. Create Sections
# - Create a large section header
st.header("Welcome to the App")
# - Write text to the main area of the app
st.write("This is a simple Streamlit app.")

# - Add a smaller sub-header
st.subheader("Features")
# - Write bullet point text (Markdown syntax works)
st.write("- Upload files")
# - Write another line of bullet point text
st.write("- Download results")


# 3. File Uploader
# - Adds a file uploader widget
# - type is a list of supported files, if None, all are supported
uploaded_file = st.file_uploader("Choose a CSV file", type=["csv"])

if uploaded_file:
	# - Read uploaded CSV into a dataframe
    df = pd.read_csv(uploaded_file)
    # - Display a sub-header h3 with markdown syntax
    st.write("### Preview of Uploaded Data:")
    # - Write dataframe as an interactive table
    st.dataframe(df)

    # 4. Simple Data Manipulation
    # - Section header for summary (h3 markdown)
    st.write("### Summary Statistics:")
    # - Displays statistics for dataframe
    st.write(df.describe())

    # 5. Download Processed File
    # - Cache the function to avoid re-computation
    @st.cache_data
    def convert_df(df):
	    # - Converts dataframe to CSV for download
        return df.to_csv(index=False).encode('utf-8')

    csv = convert_df(df)
    # - Create download button
    st.download_button(
        label="Download Processed CSV",  # Button label
        data=csv,  # Data to download
        file_name="processed_data.csv",  # File name for download
        mime="text/csv" # MIME type for CSV files
    )

# 6. Add Interactivity (Slider Example)
#  - Adds a slider to the sidebar with defined numbers
number = st.sidebar.slider("Select a number", 1, 100, 25)
# - Displays selected slider value
st.sidebar.write(f"Selected: {number}")

# 7. Show Success Message
# - Shows a green success message
st.success("App is running smoothly!")

```

### Running The App

```powershell
streamlit run your_file.py
```


## Best Practices

1. Use @st.cache_data to optimize performance for repeated computations.
    - This avoids recalculating data unnecessarily, speeding up your app.
2. Use the sidebar for inputs and options that are not frequently changed.
    - This keeps the main page clean and focused.
3. Minimize the use of heavy computations on the main thread.
    - For long-running processes, consider using asynchronous tasks or background jobs.
4. Keep the user interface minimal and intuitive.
    - Avoid clutter by showing essential information and hiding advanced options in expandable sections.
5. Document your app and include tooltips to explain unclear inputs.
    - This helps users understand how to interact with your app.

## References
1. [Streamlit Official docs](https://docs.streamlit.io/)
2. [Streamlit deployment tutorials](https://docs.streamlit.io/deploy/tutorials)