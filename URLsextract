import streamlit as st
import pandas as pd
import re

# Funktion zur Extraktion der URL aus einem iframe
def extract_iframe_url(iframe_code):
    match = re.search(r'src="([^"]+)"', iframe_code)
    if match:
        url = match.group(1)
        if "datawrapper" not in url:  # Striktere Überprüfung
            return url
    return None

# Streamlit App
st.title("Embed URL Extraktion")

# Datei-Upload
uploaded_file = st.file_uploader("Lade eine Excel-Datei hoch", type=["xlsx"])

if uploaded_file is not None:
    # Lade die Datei als DataFrame
    df = pd.read_excel(uploaded_file, engine='openpyxl')

    # Liste zum Speichern der extrahierten URLs
    embed_urls = []

    # Gehe jede Spalte durch, die "Embed" enthält
    for column in df.columns:
        if "Embed" in column:
            for value in df[column].fillna("").values:
                if isinstance(value, str):
                    if value.startswith("http") and "datawrapper" not in value:
                        embed_urls.append(value)
                    elif "<iframe" in value:
                        extracted_url = extract_iframe_url(value)
                        if extracted_url:
                            embed_urls.append(extracted_url)

    # Erstelle ein DataFrame mit den extrahierten URLs
    output_df = pd.DataFrame(embed_urls, columns=["Embed URL"])

    # Zeige die extrahierten URLs in der App
    st.write("Extrahierte URLs:")
    st.dataframe(output_df)

    # Datei-Download-Button
    output_file = "extrahierte_embeds.xlsx"
    output_df.to_excel(output_file, index=False, engine='xlsxwriter')

    with open(output_file, "rb") as file:
        st.download_button(
            label="Extrahierte URLs herunterladen",
            data=file,
            file_name=output_file,
            mime="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
        )
