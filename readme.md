Markdown

# 🍽️ Analiza Wydajności Menu i Operacji Restauracji
**Projekt Analityczny SQL | Taste of the World Café**

## 📌 1. Cel i Kontekst Biznesowy
Restauracja *Taste of the World Café* wprowadziła na początku roku nowe menu. Jako Analityk Danych otrzymałam zadanie zweryfikowania, które dania przyjęły się najlepiej, które nie przynoszą zysków oraz jakie są preferencje klientów składających zamówienia o najwyższej wartości finansowej.

---

## 🛠️ 2. Wykorzystane Narzędzia i Techniki
- **RDBMS:** Microsoft SQL Server (SSMS)
- **Zapytania i funkcje:** `LEFT JOIN`, `INNER JOIN`, `GROUP BY`, `HAVING`, `COUNT(DISTINCT)`, `WITH (CTE)`, `TRY_CAST`
- **Czyszczenie danych:** Obsługa wartości `NULL` i konwersja typów danych (`VARCHAR` do `INT`/`DECIMAL`).

---

## 📊 3. Wyniki Analizy (Kluczowe Liczby)

### A. Charakterystyka Nowego Menu
- **Łączna liczba pozycji w menu:** [32]
- **Przedział cenowy:** od $[5] do$[19.95]
- **Średnia cena dania:** $[14.47]

### B. Przegląd Wolumenu Sprzedaży
- Przeanalizowano **[5370] unikalnych zamówień**.
- Okres badania: od **[01.01.2023]** do **[31.03.2023]**.

### C. Sprzedaż według Kategorii
| Kategoria | Liczba sprzedanych dań | Łączny Przychód ($) | Średnia Cena ($) |
| :--- | :---: | :---: | :---: |
| **Asian** | [3470] | $[49560.65] \vert{}$[14.28] |
| **Italian** | [2948] | $[49462.70] \vert{}$[16.78] |
| **Mexican** | [2945] | $[41032.65] \vert{}$[13.93] |
| **American** | [2734] | $[30597.97] \vert{}$[11.19] |

### D. Bestsellery vs Najmniej Popularne Dania
- 🏆 **Top 3 Bestsellery:** [Hamburger], [Edamame], [Korean Beef Bowl]
- ⚠️ **Top 3 Najmniej Kupowane:** [Cheese Lasagna], [Potstickers], [Chicken Tacos]

---

## 💡 4. Rekomendacje dla Menedżera Restauracji

1. **Optymalizacja Menu:** Dania o najniższej sprzedaży z kategorii *Mexican* generują niepotrzebne koszty magazynowe. Zaleca się wycofanie 2 najsłabszych pozycji.
2. **Promocja Bestsellerów:** Kategorie *Italian* oraz *Asian* generują największy przychód. Warto wyeksponować je na pierwszej stronie karty dań.
3. **Oferta dla Zamówień Grupowych:** Zamówienia o najwyższej wartości opierają się głównie na zestawach dań włoskich i azjatyckich. Wprowadzenie "Zestawów Rodzinnych/Kombinowanych" zwiększy wartość średniego koszyka.

---

## 💻 5. Kod SQL
Poniżej znajduje się kluczowe zapytanie łączące tabele i analizujące preferencje TOP Klientów:

```sql
WITH TopZamowienia AS (
    SELECT TOP 5 
        order_id
    FROM OrderDetails o
    JOIN MenuItems m 
        ON o.item_id = m.menu_item_id
    GROUP BY order_id
    ORDER BY SUM(m.price) DESC
)
SELECT
    m.category
    ,COUNT(o.item_id) AS ilosc_zamowionych_dan
    ,SUM(m.price) AS przychod_od_top_klientow
FROM OrderDetails o
JOIN MenuItems m 
    ON o.item_id = m.menu_item_id
WHERE o.order_id IN (SELECT order_id FROM TopZamowienia)
GROUP BY m.category
ORDER BY ilosc_zamowionych_dan DESC;