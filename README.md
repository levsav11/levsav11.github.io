<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Моя постирочная</title>
    <!-- Подключаем скрипт Telegram Web Apps -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            margin: 0;
            padding: 16px;
        }
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
            gap: 12px;
            padding-bottom: 80px; /* Место под кнопку Telegram */
        }
        .item-card {
            background-color: var(--tg-theme-secondary-bg-color, #f0f0f0);
            border-radius: 12px;
            padding: 8px;
            text-align: center;
            cursor: pointer;
            transition: transform 0.1s;
            position: relative;
        }
        .item-card img {
            width: 100%;
            height: 100px;
            object-fit: cover;
            border-radius: 8px;
        }
        .item-card.selected {
            border: 3px solid var(--tg-theme-button-color, #2481cc);
            transform: scale(0.95);
        }
        .item-name {
            font-size: 12px;
            margin-top: 8px;
            font-weight: 500;
        }
        .badge {
            position: absolute;
            top: 4px;
            right: 4px;
            background: var(--tg-theme-button-color, #2481cc);
            color: white;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            display: none;
            align-items: center;
            justify-content: center;
            font-size: 12px;
        }
        .item-card.selected .badge {
            display: flex;
        }
    </style>
</head>
<body>

    <h2>В шкафу</h2>
    <div class="grid" id="wardrobe-grid">
        <!-- Карточки будут добавлены через JavaScript -->
    </div>

    <script>
        // Инициализация Telegram Web App
        const tg = window.Telegram.WebApp;
        tg.expand(); // Открываем на весь экран

        // Мок-данные (в будущем они будут приходить с твоего Java-бэкенда)
        const items = [
            { id: 1, name: "Худи черное", img: "https://via.placeholder.com/100" },
            { id: 2, name: "Футболка белая", img: "https://via.placeholder.com/100" },
            { id: 3, name: "Джинсы синие", img: "https://via.placeholder.com/100" },
            { id: 4, name: "Носки с утками", img: "https://via.placeholder.com/100" }
        ];

        let selectedItems = new Set();

        const grid = document.getElementById('wardrobe-grid');

        // Рендер карточек
        items.forEach(item => {
            const card = document.createElement('div');
            card.className = 'item-card';
            card.innerHTML = `
                <div class="badge">✓</div>
                <img src="${item.img}" alt="${item.name}">
                <div class="item-name">${item.name}</div>
            `;
            
            card.onclick = () => toggleSelection(card, item.id);
            grid.appendChild(card);
        });

        // Логика выделения
        function toggleSelection(cardElement, itemId) {
            tg.HapticFeedback.selectionChanged(); // Легкая вибрация телефона при клике
            
            if (selectedItems.has(itemId)) {
                selectedItems.delete(itemId);
                cardElement.classList.remove('selected');
            } else {
                selectedItems.add(itemId);
                cardElement.classList.add('selected');
            }

            updateMainButton();
        }

        // Управление главной кнопкой Telegram внизу экрана
        function updateMainButton() {
            if (selectedItems.size > 0) {
                tg.MainButton.text = `Отправить на сушилку (${selectedItems.size})`;
                tg.MainButton.show();
            } else {
                tg.MainButton.hide();
            }
        }

        // Отправка данных боту при нажатии на главную кнопку
        tg.MainButton.onClick(() => {
            const data = JSON.stringify({
                action: "move_to_drying",
                itemIds: Array.from(selectedItems)
            });
            // Отправляем данные в чат боту и закрываем окно
            tg.sendData(data);
        });
    </script>
</body>
</html>
