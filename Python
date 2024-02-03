from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import pandas as pd
import time

def get_data_from_page(driver):
    data = pd.DataFrame(columns=['Tarih', 'Isim'])

    for i in range(1, 51):
        xpath_tarih = f'/html/body/app-wos/main/div/div/div[2]/div/div/div[2]/app-input-route/app-author-summary/div/div[2]/div[2]/app-author-search-results/div[2]/app-author-search-result-card[{i}]/mat-card/div[4]/div/span[1]'
        xpath_isim = f'/html/body/app-wos/main/div/div/div[2]/div/div/div[2]/app-input-route/app-author-summary/div/div[2]/div[2]/app-author-search-results/div[2]/app-author-search-result-card[{i}]/mat-card/div[3]/mat-card-title/h3/a/span'
    
        try:
            tarih = WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.XPATH, xpath_tarih))).text
            isim = WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.XPATH, xpath_isim))).text
            data = pd.concat([data, pd.DataFrame({'Tarih': [tarih], 'Isim': [isim]})], ignore_index=True)
        except Exception as e:
            print(f'Hata: {e}')

    return data

# WebDriver'ı başlat
driver = webdriver.Chrome()

# Belirtilen sitesine git
driver.get("website to go to")

# Giriş yap
kadi = driver.find_element(By.XPATH, "//*[@id='mat-input-0']").send_keys('userName')
sifre = driver.find_element(By.XPATH, "//*[@id='mat-input-1']").send_keys('Password')
driver.find_element(By.XPATH, "//*[@id='signIn-btn']").click()

# Sayfa yüklenene kadar bekle
time.sleep(5)

cerez_button = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, '/html/body/div[4]/div[2]/div/div[2]/button')))
cerez_button.click()

time.sleep(2)

name_search_button = driver.find_element(By.CLASS_NAME, "dropdown")
name_search_button.click()

# Name Search dropdown'ı içindeki "Organization" seçeneğini bul
organization_option = WebDriverWait(driver, 5).until(EC.element_to_be_clickable((By.XPATH, "//div[contains(@class, 'wrap-mode ng-star-inserted') and contains(@class, 'ng-star-inserted')]//span[contains(text(), 'Organization')]")))

# Seçeneğe tıkla
organization_option.click()

time.sleep(2)

all_publications_radio = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, '//*[@id="mat-radio-4"]/label/span[1]')))
all_publications_radio.click()

query = driver.find_element(By.XPATH, "//*[@id='org-search']").send_keys('Ataturk University')

# Öneri olarak çıkan xpath'e tıkla
suggested_option = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, '/html/body/div[5]/div/div/div/mat-option[1]/span')))
suggested_option.click()

# Arama yapma işlemini gerçekleştir
search_button = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, '/html/body/app-wos/main/div/div/div[2]/div/div/div[2]/app-input-route/app-input-route/app-search-home/div[2]/div/app-author-search/div/div/div[2]/app-author-org-search-form/div/button[2]')))
search_button.click()

# Tüm arama sonuçlarının yüklenmesini bekleyin
WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CLASS_NAME, "ng-star-inserted")))

# Excel dosyasını oluştur
final_data = pd.DataFrame(columns=['Tarih', 'Isim'])

# İlk sayfadaki verileri al
final_data = pd.concat([final_data, get_data_from_page(driver)], ignore_index=True)

# Toplam sayfa sayısını bul
total_pages = int(driver.find_element(By.XPATH, '/html/body/app-wos/main/div/div/div[2]/div/div/div[2]/app-input-route/app-author-summary/div/div[2]/div[2]/app-author-search-results/div[1]/app-page-controls/div/form/div/span').text)

# Diğer sayfalara geçerek veri çek
for page in range(2, total_pages ):
    next_page_button = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, '/html/body/app-wos/main/div/div/div[2]/div/div/div[2]/app-input-route/app-author-summary/div/div[2]/div[2]/app-author-search-results/div[1]/app-page-controls/div/form/div/button[2]')))
    next_page_button.click()
    
    time.sleep(3)  # Sayfanın yüklenmesini bekleyin
    
    # Verileri topla ve final_data'ya ekle
    final_data = pd.concat([final_data, get_data_from_page(driver)], ignore_index=True)
    

# Excel dosyasına yaz 
final_data.to_excel('output.xlsx', index=False)

# WebDriver'ı kapat
driver.quit()
