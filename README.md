import streamlit as st
import requests
import hashlib
import time

# Konfiguracja strony
st.set_page_config(
    page_title="Snajper Dostępności",
    page_icon="🎯",
    layout="centered"
)

st.title("🎯 Snajper Dostępności i Zmian na Stronach")
st.write("Wpisz adres strony oraz słowo kluczowe, na które czekasz. Aplikacja sprawdzi, czy strona się zmieniła lub czy pojawił się poszukiwany komunikat!")

# Formularz konfiguracji celów
with st.form("target_form"):
    url = st.text_input("Adres URL do monitorowania:", value="https://example.com")
    keyword = st.text_input("Słowo kluczowe (np. 'Dostępny', 'Wolny termin', 'Bilety'):", value="Example")
    submitted = st.form_submit_button("Rozpocznij nasłuch")

# Panel kontrolny i logika sprawdzająca
if submitted:
    if not url or not keyword:
        st.error("Podaj poprawny URL oraz słowo kluczowe!")
    else:
        st.info(f"Rozpoczęto monitorowanie adresu: `{url}` pod kątem słowa: **'{keyword}'**")
        
        status_placeholder = st.empty()
        log_placeholder = st.container()

        # Symulacja inteligentnego monitorowania (w prawdziwej aplikacji tu wchodzi np. wątek lub zadanie cron)
        try:
            status_placeholder.warning("⏳ Trwa pobieranie strony i analiza...")
            
            # Pobieramy stronę za pomocą biblioteki requests (zamiast głupich ifów - obsługa nagłówków i błędów sieci)
            headers = {'User-Agent': 'Mozilla/5.0'}
            response = requests.get(url, headers=headers, timeout=5)
            
            if response.status_code == 200:
                page_content = response.text
                
                # Inteligentne sprawdzanie hashem (czy treść strony w ogóle się zmieniła)
                content_hash = hashlib.sha256(page_content.encode('utf-8')).hexdigest()
                
                with log_placeholder:
                    st.write(f"**Kod odpowiedzi HTTP:** {response.status_code}")
                    st.write(f"**Unikalny identyfikator (Hash) strony:** `{content_hash[:12]}...`")
                
                # Sprawdzamy słowo kluczowe (wielkość liter nie ma znaczenia)
                if keyword.lower() in page_content.lower():
                    status_placeholder.success(f"🎉 SUKCES! Znaleziono słowo kluczowe **'{keyword}'** na stronie!")
                    st.balloons()
                else:
                    status_placeholder.info(f"ℹ️ Strona działa, ale słowo **'{keyword}'** nie zostało jeszcze odnalezione.")
            else:
                status_placeholder.error(f"❌ Błąd serwera. Kod HTTP: {response.status_code}")
                
        except Exception as e:
            status_placeholder.error(f"❌ Nie udało się połączyć z podanym adresem: {e}")

st.divider()
st.caption("Wskazówka: W pełni automatyczna wersja tego narzędzia działa w tle przez 24/7 i wysyła powiadomienia np. na Telegram lub Discord, wykorzystując biblioteki asynchroniczne.")
