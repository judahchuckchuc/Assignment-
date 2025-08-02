
import streamlit as st
from PIL import Image
import pytesseract
import re

st.title("Arbitrage Opportunity Checker")
st.markdown("Upload a screenshot of odds to find arbitrage opportunities.")

uploaded_file = st.file_uploader("Choose an image...", type=["jpg", "png", "jpeg"])
if uploaded_file is not None:
    image = Image.open(uploaded_file)
    st.image(image, caption="Uploaded Screenshot", use_column_width=True)
    text = pytesseract.image_to_string(image)

    odds = list(map(float, re.findall(r"\b\d+\.\d+\b", text)))
    st.write("Extracted Odds:", odds)

    arbitrage_found = False
    for i in range(0, len(odds), 3):
        if i+2 < len(odds):
            inv_sum = sum(1/o for o in odds[i:i+3])
            if inv_sum < 1:
                arbitrage_found = True
                st.success(f"Arbitrage opportunity found in odds: {odds[i:i+3]} (1/{inv_sum:.2f})")

    if not arbitrage_found:
        st.warning("No arbitrage opportunities found.")
