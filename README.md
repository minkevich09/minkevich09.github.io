<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Управление широкоформатной печатью</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        .tab-btn.active {
            border-bottom: 3px solid #2563eb;
            color: #2563eb;
            font-weight: 600;
        }
        .subtab-btn.active {
            background-color: #2563eb;
            color: #ffffff;
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen flex flex-col">

    <!-- Шапка приложения -->
    <header class="bg-slate-900 text-white shadow-md">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <i class="fa-solid fa-print text-2xl text-blue-400"></i>
                <h1 class="text-xl font-bold tracking-wide">Транссфера+ <span class="text-xs font-normal text-slate-400">| Широкоформатная печать</span></h1>
            </div>
            <div class="text-sm text-slate-400">
                <i class="fa-regular fa-clock mr-1"></i> <span id="current-date"></span>
            </div>
        </div>
    </header>

    <!-- Навигационные вкладки -->
    <nav class="bg-white shadow-sm border-b border-slate-200 sticky top-0 z-10">
        <div class="container mx-auto px-4 flex overflow-x-auto space-x-8">
            <button type="button" onclick="appSwitchTab('new-order')" id="tab-new-order" class="tab-btn active py-4 px-2 text-slate-600 hover:text-blue-600 transition flex items-center gap-2 whitespace-nowrap cursor-pointer">
                <i class="fa-solid fa-plus-circle"></i> Новый заказ
            </button>
            <button type="button" onclick="appSwitchTab('orders-journal')" id="tab-orders-journal" class="tab-btn py-4 px-2 text-slate-600 hover:text-blue-600 transition flex items-center gap-2 whitespace-nowrap cursor-pointer">
                <i class="fa-solid fa-list-check"></i> Журнал заказов 
                <span id="active-orders-badge" class="bg-blue-100 text-blue-800 text-xs px-2 py-0.5 rounded-full font-bold">0</span>
            </button>
            <button type="button" onclick="appSwitchTab('database')" id="tab-database" class="tab-btn py-4 px-2 text-slate-600 hover:text-blue-600 transition flex items-center gap-2 whitespace-nowrap cursor-pointer">
                <i class="fa-solid fa-database"></i> База данных (История)
            </button>
            <button type="button" onclick="appSwitchTab('warehouse')" id="tab-warehouse" class="tab-btn py-4 px-2 text-slate-600 hover:text-blue-600 transition flex items-center gap-2 whitespace-nowrap cursor-pointer">
                <i class="fa-solid fa-boxes-stacked"></i> Склад и настройки
            </button>
            <button type="button" onclick="appSwitchTab('clients')" id="tab-clients" class="tab-btn py-4 px-2 text-slate-600 hover:text-blue-600 transition flex items-center gap-2 whitespace-nowrap cursor-pointer">
                <i class="fa-solid fa-users"></i> Контрагенты
            </button>
        </div>
    </nav>

    <!-- Основной контент -->
    <main class="container mx-auto px-4 py-6 flex-grow">

        <!-- 1. ВКЛАДКА: НОВЫЙ ЗАКАЗ -->
        <section id="sec-new-order" class="space-y-6">
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-6">
                <h2 class="text-lg font-bold text-slate-800 mb-4 pb-2 border-b border-slate-100 flex items-center gap-2">
                    <i class="fa-solid fa-file-signature text-blue-600"></i> Оформление нового заказа
                </h2>

                <form id="form-create-order" onsubmit="handleCreateOrder(event)" class="space-y-6">
                    <!-- Шапка заказа -->
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div>
                            <label class="block text-sm font-medium text-slate-700 mb-1">Контрагент *</label>
                            <div class="flex gap-2">
                                <select id="order-client" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none">
                                    <!-- Заполняется динамически -->
                                </select>
                                <button type="button" onclick="appOpenModal('modal-add-client')" class="bg-slate-200 hover:bg-slate-300 text-slate-700 px-3 py-2.5 rounded-lg transition" title="Быстро добавить контрагента">
                                    <i class="fa-solid fa-user-plus"></i>
                                </button>
                            </div>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-slate-700 mb-1">Наименование / Общее описание заказа *</label>
                            <input type="text" id="order-name" required placeholder="Например: Комплект рекламных материалов для выставки" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-slate-700 mb-1">Дата и время выполнения *</label>
                            <input type="datetime-local" id="order-due-date" required class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                    </div>

                    <!-- Позиции заказа (Множественные варианты) -->
                    <div class="border border-slate-200 rounded-lg p-4 bg-slate-50/50">
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="text-sm font-semibold text-slate-700 flex items-center gap-2">
                                <i class="fa-solid fa-layer-group text-blue-500"></i> Позиции печати (Размеры, материалы и тираж)
                            </h3>
                            <button type="button" onclick="addOrderItemRow()" class="bg-blue-100 hover:bg-blue-200 text-blue-700 text-xs font-semibold px-3 py-1.5 rounded-lg transition flex items-center gap-1">
                                <i class="fa-solid fa-plus"></i> Добавить позицию
                            </button>
                        </div>

                        <div id="order-items-container" class="space-y-3">
                            <!-- Сюда динамически добавляются строки позиций -->
                        </div>

                        <div class="mt-3 text-right text-sm font-bold text-slate-700 border-t border-slate-200 pt-2">
                            Итого общая площадь заказа: <span id="total-order-area" class="text-blue-600">0.00 м²</span>
                        </div>
                    </div>

                    <!-- Постпечатная обработка и услуги -->
                    <div class="border border-slate-200 rounded-lg p-4">
                        <h3 class="text-sm font-semibold text-slate-700 mb-3 flex items-center gap-2">
                            <i class="fa-solid fa-scissors text-blue-500"></i> Послепечатная обработка и услуги
                        </h3>
                        
                        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4 mb-2">
                            <!-- Карманы -->
                            <div class="col-span-full border-b border-slate-100 pb-3">
                                <span class="text-xs font-bold text-slate-500 uppercase tracking-wider block mb-2">Проклейка карманов</span>
                                <div class="grid grid-cols-2 sm:grid-cols-5 gap-2">
                                    <label class="inline-flex items-center text-sm cursor-pointer">
                                        <input type="checkbox" id="proc-pocket-all" onchange="toggleAllPockets(this)" class="rounded text-blue-600 focus:ring-blue-500">
                                        <span class="ml-2">Со всех сторон</span>
                                    </label>
                                    <label class="inline-flex items-center text-sm cursor-pointer">
                                        <input type="checkbox" id="proc-pocket-top" class="pocket-cb rounded text-blue-600">
                                        <span class="ml-2">Сверху</span>
                                    </label>
                                    <label class="inline-flex items-center text-sm cursor-pointer">
                                        <input type="checkbox" id="proc-pocket-bottom" class="pocket-cb rounded text-blue-600">
                                        <span class="ml-2">Снизу</span>
                                    </label>
                                    <label class="inline-flex items-center text-sm cursor-pointer">
                                        <input type="checkbox" id="proc-pocket-left" class="pocket-cb rounded text-blue-600">
                                        <span class="ml-2">Слева</span>
                                    </label>
                                    <label class="inline-flex items-center text-sm cursor-pointer">
                                        <input type="checkbox" id="proc-pocket-right" class="pocket-cb rounded text-blue-600">
                                        <span class="ml-2">Справа</span>
                                    </label>
                                </div>
                            </div>

                            <!-- Люверсы и Проклейка -->
                            <div>
                                <label class="inline-flex items-center text-sm cursor-pointer mb-1">
                                    <input type="checkbox" id="proc-eyelets-check" onchange="document.getElementById('proc-eyelets-step').disabled = !this.checked" class="rounded text-blue-600">
                                    <span class="ml-2 font-medium">Установка люверсов</span>
                                </label>
                                <input type="number" id="proc-eyelets-step" disabled placeholder="Шаг, см (напр. 30)" class="w-full border border-slate-300 rounded p-1.5 text-xs mt-1">
                            </div>

                            <div>
                                <label class="inline-flex items-center text-sm cursor-pointer block mt-1">
                                    <input type="checkbox" id="proc-glue-edges" class="rounded text-blue-600">
                                    <span class="ml-2 font-medium">Проклейка периметра</span>
                                </label>
                            </div>

                            <!-- Накатка на пластик -->
                            <div>
                                <label class="inline-flex items-center text-sm cursor-pointer mb-1">
                                    <input type="checkbox" id="proc-plastic-check" onchange="document.getElementById('proc-plastic-type').disabled = !this.checked" class="rounded text-blue-600">
                                    <span class="ml-2 font-medium">Накатать на пластик</span>
                                </label>
                                <input type="text" id="proc-plastic-type" disabled placeholder="Напр.: ПВХ 3 мм, ПЭТ 1 мм" class="w-full border border-slate-300 rounded p-1.5 text-xs mt-1">
                            </div>

                            <!-- Резка -->
                            <div>
                                <span class="text-xs font-bold text-slate-500 uppercase tracking-wider block mb-1">Резка</span>
                                <div class="space-y-1">
                                    <label class="inline-flex items-center text-sm cursor-pointer block">
                                        <input type="checkbox" id="proc-contour-cut" class="rounded text-blue-600">
                                        <span class="ml-2">Контурная резка</span>
                                    </label>
                                    <label class="inline-flex items-center text-sm cursor-pointer block">
                                        <input type="checkbox" id="proc-plotter-cut" class="rounded text-blue-600">
                                        <span class="ml-2">Плоттерная резка</span>
                                    </label>
                                </div>
                            </div>

                            <!-- Монтаж -->
                            <div class="col-span-full sm:col-span-2">
                                <label class="inline-flex items-center text-sm cursor-pointer mb-1">
                                    <input type="checkbox" id="proc-montage-check" onchange="document.getElementById('proc-montage-note').disabled = !this.checked" class="rounded text-blue-600">
                                    <span class="ml-2 font-medium">Монтажные работы</span>
                                </label>
                                <input type="text" id="proc-montage-note" disabled placeholder="Адрес или примечания по монтажу" class="w-full border border-slate-300 rounded p-1.5 text-xs mt-1">
                            </div>
                        </div>
                    </div>

                    <div class="flex justify-end">
                        <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-6 py-2.5 rounded-lg shadow-sm transition flex items-center gap-2">
                            <i class="fa-solid fa-paper-plane"></i> Отправить заказ в журнал
                        </button>
                    </div>
                </form>
            </div>
        </section>

        <!-- 2. ВКЛАДКА: ЖУРНАЛ ЗАКАЗОВ -->
        <section id="sec-orders-journal" class="hidden space-y-6">
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-6">
                <div class="flex justify-between items-center mb-4 pb-2 border-b border-slate-100">
                    <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-blue-600"></i> Текущие заказы в работе
                    </h2>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">№ / Создан</th>
                                <th class="p-3">Срок выполнения</th>
                                <th class="p-3">Контрагент</th>
                                <th class="p-3">Заказ / Позиции</th>
                                <th class="p-3">Материалы (план)</th>
                                <th class="p-3">Обработка и Услуги</th>
                                <th class="p-3">Статусы</th>
                                <th class="p-3 text-right">Действие</th>
                            </tr>
                        </thead>
                        <tbody id="journal-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 3. ВКЛАДКА: БАЗА ДАННЫХ (ИСТОРИЯ) -->
        <section id="sec-database" class="hidden space-y-6">
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-6">
                <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-4 pb-4 border-b border-slate-100">
                    <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-database text-blue-600"></i> Архив выполненных заказов
                    </h2>

                    <!-- Блок фильтрации заказов -->
                    <div class="flex flex-wrap items-center gap-3 bg-slate-50 p-3 rounded-lg border border-slate-200">
                        <div class="flex items-center gap-1.5 text-xs font-semibold text-slate-600">
                            <i class="fa-solid fa-filter text-blue-500"></i> Фильтр:
                        </div>
                        <div>
                            <select id="filter-history-year" onchange="renderDatabase()" class="bg-white border border-slate-300 rounded px-2 py-1 text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                                <option value="">Все года</option>
                            </select>
                        </div>
                        <div class="flex items-center gap-1">
                            <span class="text-xs text-slate-500">С:</span>
                            <input type="date" id="filter-history-date-from" onchange="renderDatabase()" class="bg-white border border-slate-300 rounded px-2 py-1 text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                        <div class="flex items-center gap-1">
                            <span class="text-xs text-slate-500">По:</span>
                            <input type="date" id="filter-history-date-to" onchange="renderDatabase()" class="bg-white border border-slate-300 rounded px-2 py-1 text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                        <button onclick="resetHistoryFilters()" class="text-xs text-slate-500 hover:text-red-600 underline ml-1 cursor-pointer">
                            Сбросить
                        </button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">№ / Выполнен</th>
                                <th class="p-3">Контрагент</th>
                                <th class="p-3">Заказ / Позиции</th>
                                <th class="p-3">Станок / Настройки печати</th>
                                <th class="p-3">Фактический материал</th>
                                <th class="p-3">Обработка</th>
                            </tr>
                        </thead>
                        <tbody id="db-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 4. ВКЛАДКА: СКЛАД И НАСТРОЙКИ (С ПОДВКЛАДКАМИ) -->
        <section id="sec-warehouse" class="hidden space-y-6">
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-6">
                <!-- Внутренние подвкладки -->
                <div class="flex flex-wrap items-center justify-between gap-4 mb-6 pb-3 border-b border-slate-200">
                    <div class="flex gap-2">
                        <button type="button" onclick="appSwitchSubtab('materials')" id="subtab-materials" class="subtab-btn active px-4 py-2 text-xs font-semibold rounded-lg bg-slate-100 text-slate-600 transition cursor-pointer">
                            <i class="fa-solid fa-boxes-stacked mr-1"></i> Материалы и остатки
                        </button>
                        <button type="button" onclick="appSwitchSubtab('printers')" id="subtab-printers" class="subtab-btn px-4 py-2 text-xs font-semibold rounded-lg bg-slate-100 text-slate-600 transition cursor-pointer">
                            <i class="fa-solid fa-print mr-1"></i> Виды станков
                        </button>
                        <button type="button" onclick="appSwitchSubtab('settings')" id="subtab-settings" class="subtab-btn px-4 py-2 text-xs font-semibold rounded-lg bg-slate-100 text-slate-600 transition cursor-pointer">
                            <i class="fa-solid fa-sliders mr-1"></i> Настройки печати
                        </button>
                    </div>

                    <!-- Кнопки добавления -->
                    <div id="subtab-actions">
                        <button id="btn-add-material" onclick="appOpenModal('modal-add-material')" class="bg-blue-600 hover:bg-blue-700 text-white text-xs px-3.5 py-2 rounded-lg transition flex items-center gap-1.5">
                            <i class="fa-solid fa-plus"></i> Добавить материал
                        </button>
                        <button id="btn-add-printer" onclick="appOpenModal('modal-add-printer')" class="hidden bg-blue-600 hover:bg-blue-700 text-white text-xs px-3.5 py-2 rounded-lg transition flex items-center gap-1.5">
                            <i class="fa-solid fa-plus"></i> Добавить станок
                        </button>
                        <button id="btn-add-setting" onclick="appOpenModal('modal-add-setting')" class="hidden bg-blue-600 hover:bg-blue-700 text-white text-xs px-3.5 py-2 rounded-lg transition flex items-center gap-1.5">
                            <i class="fa-solid fa-plus"></i> Добавить настройку печати
                        </button>
                    </div>
                </div>

                <!-- Подвкладка 1: Материалы -->
                <div id="subtab-content-materials" class="overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">Тип</th>
                                <th class="p-3">Наименование</th>
                                <th class="p-3">Ширина (м)</th>
                                <th class="p-3">Остаток</th>
                                <th class="p-3">Ед. изм.</th>
                                <th class="p-3 text-right">Действие</th>
                            </tr>
                        </thead>
                        <tbody id="warehouse-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>

                <!-- Подвкладка 2: Станки -->
                <div id="subtab-content-printers" class="hidden overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">Название станка / Оборудования</th>
                                <th class="p-3">Тип чернил / Технология</th>
                                <th class="p-3">Макс. ширина (м)</th>
                                <th class="p-3 text-right">Действие</th>
                            </tr>
                        </thead>
                        <tbody id="printers-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>

                <!-- Подвкладка 3: Настройки печати -->
                <div id="subtab-content-settings" class="hidden overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">Наименование настройки</th>
                                <th class="p-3">Разрешение (DPI)</th>
                                <th class="p-3">Кол-во проходов (Pass)</th>
                                <th class="p-3">Описание / Применение</th>
                                <th class="p-3 text-right">Действие</th>
                            </tr>
                        </thead>
                        <tbody id="settings-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 5. ВКЛАДКА: КОНТРАГЕНТЫ -->
        <section id="sec-clients" class="hidden space-y-6">
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-6">
                <div class="flex justify-between items-center mb-4 pb-2 border-b border-slate-100">
                    <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-users text-blue-600"></i> База контрагентов
                    </h2>
                    <button onclick="appOpenModal('modal-add-client')" class="bg-blue-600 hover:bg-blue-700 text-white text-sm px-4 py-2 rounded-lg transition flex items-center gap-2">
                        <i class="fa-solid fa-user-plus"></i> Новый контрагент
                    </button>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-xs uppercase text-slate-500 font-semibold border-b border-slate-200">
                            <tr>
                                <th class="p-3">Наименование / ФИО</th>
                                <th class="p-3">Телефон</th>
                                <th class="p-3">Email</th>
                                <th class="p-3">Замечения / Реквизиты</th>
                            </tr>
                        </thead>
                        <tbody id="clients-table-body" class="divide-y divide-slate-100">
                            <!-- Заполняется динамически -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

    </main>

    <!-- МОДАЛЬНОЕ ОКНО: Добавление контрагента -->
    <div id="modal-add-client" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-lg max-w-md w-full p-6 relative">
            <button onclick="appCloseModal('modal-add-client')" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-lg font-bold text-slate-800 mb-4">Добавить контрагента</h3>
            <form onsubmit="handleAddClient(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Название / ФИО *</label>
                    <input type="text" id="new-client-name" required class="w-full border border-slate-300 rounded-lg p-2 text-sm focus:ring-2 focus:ring-blue-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Телефон</label>
                    <input type="text" id="new-client-phone" placeholder="+7 (999) 000-00-00" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Email</label>
                    <input type="email" id="new-client-email" placeholder="client@example.com" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Заметки / Реквизиты</label>
                    <textarea id="new-client-notes" rows="2" class="w-full border border-slate-300 rounded-lg p-2 text-sm"></textarea>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                    <button type="button" onclick="appCloseModal('modal-add-client')" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium hover:bg-slate-50">Отмена</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-medium">Сохранить</button>
                </div>
            </form>
        </div>
    </div>

    <!-- МОДАЛЬНОЕ ОКНО: Добавление материала на склад -->
    <div id="modal-add-material" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-lg max-w-md w-full p-6 relative">
            <button onclick="appCloseModal('modal-add-material')" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-lg font-bold text-slate-800 mb-4">Добавить материал на склад</h3>
            <form onsubmit="handleAddMaterial(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Вид материала *</label>
                    <select id="new-mat-type" required class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                        <option value="Печать">Для печати</option>
                        <option value="Ламинация">Для ламинирования</option>
                        <option value="Пластик">Пластик / Основы</option>
                        <option value="Фурнитура">Фурнитура / Прочее</option>
                    </select>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Наименование *</label>
                    <input type="text" id="new-mat-name" required placeholder="Напр.: ПВХ Пластик 3мм" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Ширина (м)</label>
                        <input type="number" step="0.01" id="new-mat-width" placeholder="1.60" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Количество / Длина *</label>
                        <input type="number" step="0.1" id="new-mat-amount" required placeholder="50" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Единица измерения *</label>
                    <select id="new-mat-unit" required class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                        <option value="п.м.">пог. метры (п.м.)</option>
                        <option value="кв.м.">кв. метры (кв.м.)</option>
                        <option value="лист">листы (лист)</option>
                        <option value="шт">штуки (шт)</option>
                    </select>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                    <button type="button" onclick="appCloseModal('modal-add-material')" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium hover:bg-slate-50">Отмена</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-medium">Сохранить</button>
                </div>
            </form>
        </div>
    </div>

    <!-- МОДАЛЬНОЕ ОКНО: Добавление станка -->
    <div id="modal-add-printer" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-lg max-w-md w-full p-6 relative">
            <button onclick="appCloseModal('modal-add-printer')" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-lg font-bold text-slate-800 mb-4">Добавить новое оборудование</h3>
            <form onsubmit="handleAddPrinter(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Название станка *</label>
                    <input type="text" id="new-printer-name" required placeholder="Напр.: Mimaki SWJ-320S" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Тип чернил / Технология *</label>
                    <input type="text" id="new-printer-tech" required placeholder="Напр.: Экосольвент, УФ, Сольвент" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Максимальная ширина печати (м)</label>
                    <input type="number" step="0.01" id="new-printer-width" placeholder="3.20" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div class="flex justify-end gap-2 pt-2">
                    <button type="button" onclick="appCloseModal('modal-add-printer')" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium hover:bg-slate-50">Отмена</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-medium">Сохранить</button>
                </div>
            </form>
        </div>
    </div>

    <!-- МОДАЛЬНОЕ ОКНО: Добавление настройки печати -->
    <div id="modal-add-setting" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-lg max-w-md w-full p-6 relative">
            <button onclick="appCloseModal('modal-add-setting')" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-lg font-bold text-slate-800 mb-4">Добавить настройку печати</h3>
            <form onsubmit="handleAddPrintSetting(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Наименование пресета *</label>
                    <input type="text" id="new-setting-name" required placeholder="Напр.: Интерьерная высококачественная" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Разрешение (DPI)</label>
                        <input type="text" id="new-setting-dpi" placeholder="1440 DPI" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Проходы (Pass)</label>
                        <input type="text" id="new-setting-pass" placeholder="8 Pass" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Описание / Применение</label>
                    <input type="text" id="new-setting-desc" placeholder="Для близкого рассматривания, мелкий текст" class="w-full border border-slate-300 rounded-lg p-2 text-sm">
                </div>
                <div class="flex justify-end gap-2 pt-2">
                    <button type="button" onclick="appCloseModal('modal-add-setting')" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium hover:bg-slate-50">Отмена</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm font-medium">Сохранить</button>
                </div>
            </form>
        </div>
    </div>

    <!-- МОДАЛЬНОЕ ОКНО: Списание и завершение заказа -->
    <div id="modal-complete-order" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-lg max-w-lg w-full p-6 relative">
            <button onclick="appCloseModal('modal-complete-order')" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
            <h3 class="text-lg font-bold text-slate-800 mb-2">Завершение и списание заказа</h3>
            <p id="complete-order-subtitle" class="text-xs text-slate-500 mb-4"></p>

            <form onsubmit="handleFinalizeOrder(event)" class="space-y-4">
                <input type="hidden" id="complete-order-id">
                
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Вид станка (оборудование) *</label>
                    <select id="complete-printer" required class="w-full border border-slate-300 rounded-lg p-2.5 text-sm">
                        <!-- Динамически заполняется из базы станков -->
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Настройки печати (режим / качество) *</label>
                    <select id="complete-print-setting" required class="w-full border border-slate-300 rounded-lg p-2.5 text-sm">
                        <!-- Динамически заполняется из настроек печати -->
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Материал печати (по факту) *</label>
                    <select id="complete-actual-material" required class="w-full border border-slate-300 rounded-lg p-2.5 text-sm">
                        <!-- Заполняется динамически -->
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Материал ламинации (по факту)</label>
                    <select id="complete-actual-lam" class="w-full border border-slate-300 rounded-lg p-2.5 text-sm">
                        <option value="Нет">Без ламинации</option>
                        <!-- Заполняется динамически -->
                    </select>
                </div>

                <div class="bg-amber-50 border border-amber-200 rounded-lg p-3 text-xs text-amber-800 flex gap-2">
                    <i class="fa-solid fa-triangle-exclamation text-amber-600 text-sm mt-0.5"></i>
                    <div>После подтверждения заказ спишется со склада, удалится из активного журнала и попадет в историческую базу данных.</div>
                </div>

                <div class="flex justify-end gap-2 pt-2">
                    <button type="button" onclick="appCloseModal('modal-complete-order')" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium hover:bg-slate-50">Отмена</button>
                    <button type="submit" class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white rounded-lg text-sm font-medium flex items-center gap-1">
                        <i class="fa-solid fa-check"></i> Подтвердить и Передать в БД
                    </button>
                </div>
            </form>
        </div>
    </div>


    <!-- СКРИПТЫ ПРИЛОЖЕНИЯ -->
    <script>
        let itemRowCounter = 0;

        // Безопасное хранилище
        const db = {
            getClients: () => { try { return JSON.parse(localStorage.getItem('pw_clients') || '[]'); } catch(e) { return []; } },
            saveClients: (data) => localStorage.setItem('pw_clients', JSON.stringify(data)),
            
            getMaterials: () => { try { return JSON.parse(localStorage.getItem('pw_materials') || '[]'); } catch(e) { return []; } },
            saveMaterials: (data) => localStorage.setItem('pw_materials', JSON.stringify(data)),

            getPrinters: () => { try { return JSON.parse(localStorage.getItem('pw_printers') || '[]'); } catch(e) { return []; } },
            savePrinters: (data) => localStorage.setItem('pw_printers', JSON.stringify(data)),

            getPrintSettings: () => { try { return JSON.parse(localStorage.getItem('pw_print_settings') || '[]'); } catch(e) { return []; } },
            savePrintSettings: (data) => localStorage.setItem('pw_print_settings', JSON.stringify(data)),

            getOrders: () => { try { return JSON.parse(localStorage.getItem('pw_orders') || '[]'); } catch(e) { return []; } },
            saveOrders: (data) => localStorage.setItem('pw_orders', JSON.stringify(data)),

            getHistory: () => { try { return JSON.parse(localStorage.getItem('pw_history') || '[]'); } catch(e) { return []; } },
            saveHistory: (data) => localStorage.setItem('pw_history', JSON.stringify(data)),
        };

        // Инициализация стартовых данных
        function initData() {
            if (!localStorage.getItem('pw_clients')) {
                const defaultClients = [
                    { id: 1, name: 'ООО "Рекламный Мир"', phone: '+7 (495) 123-45-67', email: 'info@recworld.ru', notes: 'Постоянный клиент' },
                    { id: 2, name: 'ИП Иванов А.В.', phone: '+7 (916) 987-65-43', email: 'ivanov@mail.ru', notes: 'Оплата по б/н' }
                ];
                db.saveClients(defaultClients);
            }

            if (!localStorage.getItem('pw_materials')) {
                const defaultMaterials = [
                    { id: 1, type: 'Печать', name: 'Баннер Frontlit 440г', width: 3.2, amount: 100, unit: 'п.м.' },
                    { id: 2, type: 'Печать', name: 'Пленка Самоклеящаяся Orajet 3640', width: 1.52, amount: 50, unit: 'п.м.' },
                    { id: 3, type: 'Печать', name: 'Бумага Ситилайт 150г', width: 1.27, amount: 80, unit: 'п.м.' },
                    { id: 4, type: 'Ламинация', name: 'Пленка для ламинации Глянцевая 80 мкм', width: 1.52, amount: 45, unit: 'п.м.' },
                    { id: 5, type: 'Ламинация', name: 'Пленка для ламинации Матовая 80 мкм', width: 1.05, amount: 30, unit: 'п.м.' },
                    { id: 6, type: 'Пластик', name: 'ПВХ 3мм белый', width: '-', amount: 20, unit: 'лист' },
                    { id: 7, type: 'Фурнитура', name: 'Люверсы 10 мм', width: '-', amount: 2000, unit: 'шт' }
                ];
                db.saveMaterials(defaultMaterials);
            }

            if (!localStorage.getItem('pw_printers')) {
                const defaultPrinters = [
                    { id: 1, name: 'Roland Soljet EJ-640', tech: 'Экосольвент', width: 1.62 },
                    { id: 2, name: 'Flora LJ 320P', tech: 'Сольвент', width: 3.20 },
                    { id: 3, name: 'Mimaki UV UCJV300-160', tech: 'УФ печать', width: 1.60 },
                    { id: 4, name: 'Graphtec FC9000-140', tech: 'Плоттерный резчик', width: 1.40 }
                ];
                db.savePrinters(defaultPrinters);
            }

            if (!localStorage.getItem('pw_print_settings')) {
                const defaultSettings = [
                    { id: 1, name: 'Стандарт (720x720 dpi, 4 Pass)', dpi: '720x720', pass: '4 Pass', desc: 'Для наружной рекламы и баннеров' },
                    { id: 2, name: 'Интерьерная (1440x1440 dpi, 8 Pass)', dpi: '1440x1440', pass: '8 Pass', desc: 'Для близкого контакта и высокого качества' },
                    { id: 3, name: 'Скоростной режим (360x720 dpi, 2 Pass)', dpi: '360x720', pass: '2 Pass', desc: 'Черновая магистральная печать' },
                    { id: 4, name: 'УФ Макс. плотность + Белый', dpi: '1200x1200', pass: '12 Pass', desc: 'Печать на прозрачных и темных материалах' }
                ];
                db.savePrintSettings(defaultSettings);
            }

            if (!localStorage.getItem('pw_orders')) db.saveOrders([]);
            if (!localStorage.getItem('pw_history')) db.saveHistory([]);
        }

        // Запуск при старте
        document.addEventListener('DOMContentLoaded', () => {
            initData();
            document.getElementById('current-date').innerText = new Date().toLocaleDateString('ru-RU');
            populateSelects();
            addOrderItemRow();
            renderJournal();
            renderDatabase();
            renderWarehouse();
            renderPrinters();
            renderPrintSettings();
            renderClients();
            updateBadge();
            
            // Установка дефолтного срока выполнения (завтрашний день)
            const tomorrow = new Date();
            tomorrow.setDate(tomorrow.getDate() + 1);
            tomorrow.setHours(18, 0, 0, 0);
            const isoStr = new Date(tomorrow.getTime() - (tomorrow.getTimezoneOffset() * 60000)).toISOString().slice(0, 16);
            document.getElementById('order-due-date').value = isoStr;
        });

        // Навигация по главным вкладкам
        function appSwitchTab(tabName) {
            document.querySelectorAll('main > section').forEach(sec => sec.classList.add('hidden'));
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));

            const targetSection = document.getElementById(`sec-${tabName}`);
            const targetTab = document.getElementById(`tab-${tabName}`);
            
            if (targetSection) targetSection.classList.remove('hidden');
            if (targetTab) targetTab.classList.add('active');
        }

        // Переключение подвкладок склада
        function appSwitchSubtab(subtabName) {
            document.getElementById('subtab-content-materials').classList.add('hidden');
            document.getElementById('subtab-content-printers').classList.add('hidden');
            document.getElementById('subtab-content-settings').classList.add('hidden');

            document.getElementById('subtab-materials').classList.remove('active');
            document.getElementById('subtab-printers').classList.remove('active');
            document.getElementById('subtab-settings').classList.remove('active');

            document.getElementById('btn-add-material').classList.add('hidden');
            document.getElementById('btn-add-printer').classList.add('hidden');
            document.getElementById('btn-add-setting').classList.add('hidden');

            document.getElementById(`subtab-content-${subtabName}`).classList.remove('hidden');
            document.getElementById(`subtab-${subtabName}`).classList.add('active');

            if (subtabName === 'materials') document.getElementById('btn-add-material').classList.remove('hidden');
            if (subtabName === 'printers') document.getElementById('btn-add-printer').classList.remove('hidden');
            if (subtabName === 'settings') document.getElementById('btn-add-setting').classList.remove('hidden');
        }

        // Управление модальными окнами
        function appOpenModal(id) {
            const el = document.getElementById(id);
            if (el) {
                el.classList.remove('hidden');
                el.classList.add('flex');
            }
        }

        function appCloseModal(id) {
            const el = document.getElementById(id);
            if (el) {
                el.classList.add('hidden');
                el.classList.remove('flex');
            }
        }

        // Добавление строки позиции
        function addOrderItemRow() {
            itemRowCounter++;
            const container = document.getElementById('order-items-container');
            const materials = db.getMaterials();

            const printOpts = materials.filter(m => m.type === 'Печать')
                .map(m => `<option value="${m.name}">${m.name} (${m.width}m)</option>`).join('');
            const lamOpts = materials.filter(m => m.type === 'Ламинация')
                .map(m => `<option value="${m.name}">${m.name} (${m.width}m)</option>`).join('');

            const row = document.createElement('div');
            row.id = `item-row-${itemRowCounter}`;
            row.className = "bg-white p-3 rounded-lg border border-slate-200 shadow-sm space-y-2 item-row";
            row.innerHTML = `
                <div class="flex justify-between items-center pb-1 border-b border-slate-100">
                    <span class="text-xs font-bold text-slate-500 item-title">Позиция</span>
                    <button type="button" onclick="removeOrderItemRow('${row.id}')" class="text-red-500 hover:text-red-700 text-xs font-medium">
                        <i class="fa-solid fa-trash-can"></i> Удалить
                    </button>
                </div>
                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-5 gap-3">
                    <div>
                        <label class="block text-xs text-slate-500 mb-0.5">Ширина (мм) *</label>
                        <input type="number" required min="1" placeholder="1000" oninput="calculateTotalArea()" class="item-width w-full border border-slate-300 rounded p-1.5 text-xs">
                    </div>
                    <div>
                        <label class="block text-xs text-slate-500 mb-0.5">Высота (мм) *</label>
                        <input type="number" required min="1" placeholder="2000" oninput="calculateTotalArea()" class="item-height w-full border border-slate-300 rounded p-1.5 text-xs">
                    </div>
                    <div>
                        <label class="block text-xs text-slate-500 mb-0.5">Тираж (шт) *</label>
                        <input type="number" required min="1" value="1" oninput="calculateTotalArea()" class="item-qty w-full border border-slate-300 rounded p-1.5 text-xs">
                    </div>
                    <div>
                        <label class="block text-xs text-slate-500 mb-0.5">Печать *</label>
                        <select required class="item-print w-full border border-slate-300 rounded p-1.5 text-xs">
                            ${printOpts || '<option value="Баннер">Баннер</option>'}
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs text-slate-500 mb-0.5">Ламинация</label>
                        <select class="item-lam w-full border border-slate-300 rounded p-1.5 text-xs">
                            <option value="Без ламинации">-- Без ламинации --</option>
                            ${lamOpts}
                        </select>
                    </div>
                </div>
                <div class="text-right text-xs text-slate-400">
                    Площадь позиции: <span class="item-area font-bold text-slate-600">0.00 м²</span>
                </div>
            `;

            container.appendChild(row);
            renumberItems();
            calculateTotalArea();
        }

        // Перенумерация
        function renumberItems() {
            const rows = document.querySelectorAll('.item-row');
            rows.forEach((row, index) => {
                const title = row.querySelector('.item-title');
                if (title) title.innerText = `Позиция #${index + 1}`;
            });
        }

        // Удаление строки позиции
        function removeOrderItemRow(rowId) {
            const rows = document.querySelectorAll('.item-row');
            if (rows.length <= 1) {
                alert('В заказе должна быть хотя бы одна позиция');
                return;
            }
            const row = document.getElementById(rowId);
            if (row) {
                row.remove();
                renumberItems();
                calculateTotalArea();
            }
        }

        // Расчет площади
        function calculateTotalArea() {
            let totalArea = 0;
            const rows = document.querySelectorAll('.item-row');

            rows.forEach(row => {
                const w = parseFloat(row.querySelector('.item-width').value) || 0;
                const h = parseFloat(row.querySelector('.item-height').value) || 0;
                const q = parseInt(row.querySelector('.item-qty').value) || 0;

                const rowArea = (w / 1000) * (h / 1000) * q;
                row.querySelector('.item-area').innerText = rowArea.toFixed(2) + ' м²';
                totalArea += rowArea;
            });

            document.getElementById('total-order-area').innerText = totalArea.toFixed(2) + ' м²';
        }

        function toggleAllPockets(source) {
            document.querySelectorAll('.pocket-cb').forEach(cb => cb.checked = source.checked);
        }

        // Заполнение селектов
        function populateSelects() {
            // Контрагенты
            const clientSelect = document.getElementById('order-client');
            const clients = db.getClients();
            if (clients.length === 0) {
                clientSelect.innerHTML = '<option value="Без контрагента">-- Нет контрагентов --</option>';
            } else {
                clientSelect.innerHTML = clients.map(c => `<option value="${c.name}">${c.name}</option>`).join('');
            }

            // Материалы
            const materials = db.getMaterials();
            const actualPrintSelect = document.getElementById('complete-actual-material');
            const actualLamSelect = document.getElementById('complete-actual-lam');

            const printOpts = materials.filter(m => m.type === 'Печать').map(m => `<option value="${m.name}">${m.name} (${m.width}m)</option>`).join('');
            const lamOpts = materials.filter(m => m.type === 'Ламинация').map(m => `<option value="${m.name}">${m.name} (${m.width}m)</option>`).join('');

            if (actualPrintSelect) actualPrintSelect.innerHTML = printOpts || '<option value="Баннер">Баннер</option>';
            if (actualLamSelect) actualLamSelect.innerHTML = '<option value="Нет">Без ламинации</option>' + lamOpts;

            // Станки
            const printerSelect = document.getElementById('complete-printer');
            const printers = db.getPrinters();
            if (printerSelect) printerSelect.innerHTML = printers.map(p => `<option value="${p.name} (${p.tech})">${p.name} (${p.tech})</option>`).join('');

            // Настройки печати
            const settingsSelect = document.getElementById('complete-print-setting');
            const settings = db.getPrintSettings();
            if (settingsSelect) settingsSelect.innerHTML = settings.map(s => `<option value="${s.name}">${s.name}</option>`).join('');
        }

        // Вспомогательная функция форматирования даты из ISO в Читаемый
        function formatDateToDisplay(isoStr) {
            if (!isoStr) return '-';
            const d = new Date(isoStr);
            if (isNaN(d.getTime())) return isoStr;
            return d.toLocaleString('ru-RU', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
        }

        // Создание заказа
        function handleCreateOrder(e) {
            e.preventDefault();

            const items = [];
            let totalArea = 0;
            document.querySelectorAll('.item-row').forEach(row => {
                const w = parseFloat(row.querySelector('.item-width').value) || 0;
                const h = parseFloat(row.querySelector('.item-height').value) || 0;
                const q = parseInt(row.querySelector('.item-qty').value) || 1;
                const area = parseFloat(((w / 1000) * (h / 1000) * q).toFixed(2));
                totalArea += area;

                items.push({
                    width: w,
                    height: h,
                    quantity: q,
                    printMaterial: row.querySelector('.item-print').value,
                    lamMaterial: row.querySelector('.item-lam').value,
                    area: area
                });
            });

            const pockets = [];
            if (document.getElementById('proc-pocket-top').checked) pockets.push('Сверху');
            if (document.getElementById('proc-pocket-bottom').checked) pockets.push('Снизу');
            if (document.getElementById('proc-pocket-left').checked) pockets.push('Слева');
            if (document.getElementById('proc-pocket-right').checked) pockets.push('Справа');

            let pocketText = 'Нет';
            if (document.getElementById('proc-pocket-all').checked || pockets.length === 4) {
                pocketText = 'Периметр (со всех сторон)';
            } else if (pockets.length > 0) {
                pocketText = pockets.join(', ');
            }

            const eyelets = document.getElementById('proc-eyelets-check').checked;
            const eyeletsStep = document.getElementById('proc-eyelets-step').value;
            const glueEdges = document.getElementById('proc-glue-edges').checked;
            const contourCut = document.getElementById('proc-contour-cut').checked;
            const plotterCut = document.getElementById('proc-plotter-cut').checked;
            const plasticCheck = document.getElementById('proc-plastic-check').checked;
            const plasticType = document.getElementById('proc-plastic-type').value;
            const montageCheck = document.getElementById('proc-montage-check').checked;
            const montageNote = document.getElementById('proc-montage-note').value;

            const dueDateValue = document.getElementById('order-due-date').value;

            const newOrder = {
                id: 'ORD-' + Date.now().toString().slice(-5),
                date: new Date().toISOString(),
                dueDate: dueDateValue ? new Date(dueDateValue).toISOString() : null,
                client: document.getElementById('order-client').value,
                name: document.getElementById('order-name').value,
                items: items,
                totalArea: totalArea.toFixed(2),
                processing: {
                    pockets: pocketText,
                    eyelets: eyelets ? `Да (шаг ${eyeletsStep || 30}см)` : 'Нет',
                    glueEdges: glueEdges ? 'Да' : 'Нет',
                    contourCut: contourCut ? 'Да' : 'Нет',
                    plotterCut: plotterCut ? 'Да' : 'Нет',
                    plastic: plasticCheck ? `Да (${plasticType || 'ПВХ'})` : 'Нет',
                    montage: montageCheck ? `Да (${montageNote || 'без примечаний'})` : 'Нет'
                },
                printed: false,
                laminated: false
            };

            const orders = db.getOrders();
            orders.unshift(newOrder);
            db.saveOrders(orders);

            document.getElementById('form-create-order').reset();
            document.getElementById('order-items-container').innerHTML = '';
            addOrderItemRow();

            // Снова заполняем завтрашний день в поле даты
            const tomorrow = new Date();
            tomorrow.setDate(tomorrow.getDate() + 1);
            tomorrow.setHours(18, 0, 0, 0);
            document.getElementById('order-due-date').value = new Date(tomorrow.getTime() - (tomorrow.getTimezoneOffset() * 60000)).toISOString().slice(0, 16);

            renderJournal();
            updateBadge();
            appSwitchTab('orders-journal');
        }

        // Рендер журнала
        function renderJournal() {
            const orders = db.getOrders();
            const tbody = document.getElementById('journal-table-body');

            if (orders.length === 0) {
                tbody.innerHTML = `<tr><td colspan="8" class="p-4 text-center text-slate-400">Нет активных заказов в работе</td></tr>`;
                return;
            }

            tbody.innerHTML = orders.map((o, index) => {
                const procList = [];
                if (o.processing.pockets !== 'Нет') procList.push(`Карманы: ${o.processing.pockets}`);
                if (o.processing.eyelets !== 'Нет') procList.push(`Люверсы: ${o.processing.eyelets}`);
                if (o.processing.glueEdges === 'Да') procList.push(`Проклейка края`);
                if (o.processing.plastic !== 'Нет') procList.push(`Накатка на пластик: ${o.processing.plastic}`);
                if (o.processing.contourCut === 'Да') procList.push(`Контурная резка`);
                if (o.processing.plotterCut === 'Да') procList.push(`Плоттерная резка`);
                if (o.processing.montage !== 'Нет') procList.push(`Монтаж: ${o.processing.montage}`);

                const itemsHtml = o.items.map((it, idx) => `
                    <div class="border-b border-slate-100 last:border-0 pb-1 mb-1">
                        <span class="font-semibold text-slate-700">#${idx+1}: ${it.width}x${it.height} мм</span> - ${it.quantity} шт (${it.area} м²)
                    </div>
                `).join('');

                const materialsHtml = o.items.map((it, idx) => `
                    <div class="border-b border-slate-100 last:border-0 pb-1 mb-1 text-xs">
                        <span class="text-slate-400">#${idx+1}:</span> ${it.printMaterial} ${it.lamMaterial !== 'Без ламинации' ? `+ ${it.lamMaterial}` : ''}
                    </div>
                `).join('');

                return `
                    <tr class="hover:bg-slate-50">
                        <td class="p-3">
                            <span class="font-bold text-slate-800">${o.id}</span>
                            <div class="text-xs text-slate-400">${formatDateToDisplay(o.date)}</div>
                        </td>
                        <td class="p-3">
                            <div class="text-xs font-semibold text-amber-700 bg-amber-50 px-2 py-1 rounded border border-amber-200 inline-block">
                                <i class="fa-regular fa-clock mr-1"></i>${formatDateToDisplay(o.dueDate)}
                            </div>
                        </td>
                        <td class="p-3 font-medium text-slate-700">${o.client}</td>
                        <td class="p-3">
                            <div class="font-semibold text-slate-800 mb-1">${o.name}</div>
                            <div class="text-xs text-slate-600 bg-slate-50 p-2 rounded border border-slate-200">
                                ${itemsHtml}
                                <div class="font-bold text-slate-700 pt-1">Всего: ${o.totalArea} м²</div>
                            </div>
                        </td>
                        <td class="p-3">
                            <div class="bg-slate-50 p-2 rounded border border-slate-200">
                                ${materialsHtml}
                            </div>
                        </td>
                        <td class="p-3 text-xs">
                            ${procList.length > 0 ? procList.map(p => `<span class="inline-block bg-slate-100 border border-slate-200 rounded px-1.5 py-0.5 mr-1 mb-1">${p}</span>`).join('') : '<span class="text-slate-400">Без обработки</span>'}
                        </td>
                        <td class="p-3 space-y-1">
                            <label class="flex items-center gap-1.5 text-xs cursor-pointer">
                                <input type="checkbox" ${o.printed ? 'checked' : ''} onchange="toggleOrderStatus(${index}, 'printed')" class="rounded text-blue-600">
                                <span class="${o.printed ? 'text-green-600 font-semibold' : 'text-slate-500'}">Отпечатано</span>
                            </label>
                            <label class="flex items-center gap-1.5 text-xs cursor-pointer">
                                <input type="checkbox" ${o.laminated ? 'checked' : ''} onchange="toggleOrderStatus(${index}, 'laminated')" class="rounded text-blue-600">
                                <span class="${o.laminated ? 'text-green-600 font-semibold' : 'text-slate-500'}">Заламинировано</span>
                            </label>
                        </td>
                        <td class="p-3 text-right">
                            <button onclick="openCompleteModal(${index})" class="bg-green-600 hover:bg-green-700 text-white text-xs px-3 py-1.5 rounded transition flex items-center gap-1 ml-auto cursor-pointer">
                                <i class="fa-solid fa-box-archive"></i> Списать / В БД
                            </button>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        function toggleOrderStatus(index, field) {
            const orders = db.getOrders();
            orders[index][field] = !orders[index][field];
            db.saveOrders(orders);
            renderJournal();
        }

        function openCompleteModal(index) {
            const orders = db.getOrders();
            const order = orders[index];

            document.getElementById('complete-order-id').value = order.id;
            document.getElementById('complete-order-subtitle').innerText = `Заказ: ${order.id} - ${order.name} (${order.client})`;
            
            if (order.items && order.items.length > 0) {
                document.getElementById('complete-actual-material').value = order.items[0].printMaterial;
                if (order.items[0].lamMaterial && order.items[0].lamMaterial !== 'Без ламинации') {
                    document.getElementById('complete-actual-lam').value = order.items[0].lamMaterial;
                } else {
                    document.getElementById('complete-actual-lam').value = 'Нет';
                }
            }

            appOpenModal('modal-complete-order');
        }

        function handleFinalizeOrder(e) {
            e.preventDefault();
            const orderId = document.getElementById('complete-order-id').value;
            
            let orders = db.getOrders();
            const orderIndex = orders.findIndex(o => o.id === orderId);
            if (orderIndex === -1) return;

            const order = orders[orderIndex];

            const completedOrder = {
                ...order,
                completedDate: new Date().toISOString(),
                printerUsed: document.getElementById('complete-printer').value,
                printSettingUsed: document.getElementById('complete-print-setting').value,
                actualMaterial: document.getElementById('complete-actual-material').value,
                actualLam: document.getElementById('complete-actual-lam').value
            };

            const history = db.getHistory();
            history.unshift(completedOrder);
            db.saveHistory(history);

            orders.splice(orderIndex, 1);
            db.saveOrders(orders);

            appCloseModal('modal-complete-order');
            renderJournal();
            renderDatabase();
            updateBadge();
        }

        // Обновление списка годов в фильтре истории
        function updateHistoryYearOptions(history) {
            const yearSelect = document.getElementById('filter-history-year');
            const currentSelectedYear = yearSelect.value;
            
            const years = new Set();
            history.forEach(h => {
                const dateVal = h.completedDate || h.date;
                if (dateVal) {
                    const year = new Date(dateVal).getFullYear();
                    if (!isNaN(year)) years.add(year);
                }
            });

            const sortedYears = Array.from(years).sort((a, b) => b - a);
            yearSelect.innerHTML = '<option value="">Все года</option>' + 
                sortedYears.map(y => `<option value="${y}">${y}</option>`).join('');
                
            yearSelect.value = currentSelectedYear;
        }

        // Сброс фильтров в архиве
        function resetHistoryFilters() {
            document.getElementById('filter-history-year').value = '';
            document.getElementById('filter-history-date-from').value = '';
            document.getElementById('filter-history-date-to').value = '';
            renderDatabase();
        }

        // Рендер базы данных с поддержкой фильтров по году и датам
        function renderDatabase() {
            const history = db.getHistory();
            updateHistoryYearOptions(history);

            const yearFilter = document.getElementById('filter-history-year').value;
            const dateFromFilter = document.getElementById('filter-history-date-from').value;
            const dateToFilter = document.getElementById('filter-history-date-to').value;

            const filteredHistory = history.filter(h => {
                const dateObj = new Date(h.completedDate || h.date);
                if (isNaN(dateObj.getTime())) return true;

                // Фильтр по году
                if (yearFilter && dateObj.getFullYear().toString() !== yearFilter) {
                    return false;
                }

                // Фильтр по диапазону дат
                if (dateFromFilter) {
                    const fromDate = new Date(dateFromFilter);
                    fromDate.setHours(0, 0, 0, 0);
                    if (dateObj < fromDate) return false;
                }

                if (dateToFilter) {
                    const toDate = new Date(dateToFilter);
                    toDate.setHours(23, 59, 59, 999);
                    if (dateObj > toDate) return false;
                }

                return true;
            });

            const tbody = document.getElementById('db-table-body');

            if (filteredHistory.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="p-4 text-center text-slate-400">Записи не найдены или история выполнений пуста</td></tr>`;
                return;
            }

            tbody.innerHTML = filteredHistory.map(h => {
                const procList = [];
                if (h.processing.pockets !== 'Нет') procList.push(`Карманы: ${h.processing.pockets}`);
                if (h.processing.eyelets !== 'Нет') procList.push(`Люверсы: ${h.processing.eyelets}`);
                if (h.processing.glueEdges === 'Да') procList.push(`Проклейка края`);
                if (h.processing.plastic !== 'Нет') procList.push(`Накатка на пластик: ${h.processing.plastic}`);
                if (h.processing.contourCut === 'Да') procList.push(`Контурная резка`);
                if (h.processing.plotterCut === 'Да') procList.push(`Плоттерная резка`);
                if (h.processing.montage !== 'Нет') procList.push(`Монтаж: ${h.processing.montage}`);

                const itemsHtml = h.items ? h.items.map((it, idx) => `
                    <div class="border-b border-slate-100 last:border-0 pb-1 mb-1">
                        <span class="font-semibold text-slate-700">#${idx+1}: ${it.width}x${it.height} мм</span> - ${it.quantity} шт (${it.area} м²)
                    </div>
                `).join('') : '';

                return `
                    <tr class="hover:bg-slate-50">
                        <td class="p-3">
                            <span class="font-bold text-slate-800">${h.id}</span>
                            <div class="text-xs text-slate-400">${formatDateToDisplay(h.completedDate || h.date)}</div>
                        </td>
                        <td class="p-3 font-medium text-slate-700">${h.client}</td>
                        <td class="p-3">
                            <div class="font-semibold text-slate-800 mb-1">${h.name}</div>
                            <div class="text-xs text-slate-600 bg-slate-50 p-2 rounded border border-slate-200">
                                ${itemsHtml}
                                <div class="font-bold text-slate-700 pt-1">Всего: ${h.totalArea || h.area} м²</div>
                            </div>
                        </td>
                        <td class="p-3 text-xs">
                            <div class="font-semibold text-blue-800"><i class="fa-solid fa-print mr-1"></i> ${h.printerUsed}</div>
                            <div class="text-slate-500 mt-1"><i class="fa-solid fa-sliders mr-1"></i> ${h.printSettingUsed || 'Стандарт'}</div>
                        </td>
                        <td class="p-3 text-xs">
                            <div><span class="text-slate-400">Печать:</span> ${h.actualMaterial}</div>
                            <div><span class="text-slate-400">Ламинация:</span> ${h.actualLam}</div>
                        </td>
                        <td class="p-3 text-xs">
                            ${procList.length > 0 ? procList.map(p => `<span class="inline-block bg-slate-100 border border-slate-200 rounded px-1.5 py-0.5 mr-1 mb-1">${p}</span>`).join('') : '<span class="text-slate-400">Без обработки</span>'}
                        </td>
                    </tr>
                `;
            }).join('');
        }

        // Склад, станки и настройки
        function renderWarehouse() {
            const materials = db.getMaterials();
            const tbody = document.getElementById('warehouse-table-body');

            tbody.innerHTML = materials.map(m => `
                <tr class="hover:bg-slate-50">
                    <td class="p-3">
                        <span class="inline-block px-2 py-0.5 text-xs font-semibold rounded ${
                            m.type === 'Печать' ? 'bg-blue-100 text-blue-800' :
                            m.type === 'Ламинация' ? 'bg-purple-100 text-purple-800' :
                            m.type === 'Пластик' ? 'bg-teal-100 text-teal-800' : 'bg-amber-100 text-amber-800'
                        }">
                            ${m.type}
                        </span>
                    </td>
                    <td class="p-3 font-semibold text-slate-800">${m.name}</td>
                    <td class="p-3 text-slate-600">${m.width} ${m.width !== '-' ? 'м' : ''}</td>
                    <td class="p-3 font-bold ${m.amount < 20 ? 'text-red-600' : 'text-slate-800'}">${m.amount}</td>
                    <td class="p-3 text-slate-500 text-xs">${m.unit}</td>
                    <td class="p-3 text-right">
                        <button onclick="deleteMaterial(${m.id})" class="text-red-500 hover:text-red-700 text-xs font-medium cursor-pointer">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        function renderPrinters() {
            const printers = db.getPrinters();
            const tbody = document.getElementById('printers-table-body');

            tbody.innerHTML = printers.map(p => `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold text-slate-800">${p.name}</td>
                    <td class="p-3 text-slate-600">${p.tech}</td>
                    <td class="p-3 text-slate-600">${p.width ? p.width + ' м' : '-'}</td>
                    <td class="p-3 text-right">
                        <button onclick="deletePrinter(${p.id})" class="text-red-500 hover:text-red-700 text-xs font-medium cursor-pointer">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        function renderPrintSettings() {
            const settings = db.getPrintSettings();
            const tbody = document.getElementById('settings-table-body');

            tbody.innerHTML = settings.map(s => `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold text-slate-800">${s.name}</td>
                    <td class="p-3 text-slate-600">${s.dpi || '-'}</td>
                    <td class="p-3 text-slate-600">${s.pass || '-'}</td>
                    <td class="p-3 text-xs text-slate-500">${s.desc || '-'}</td>
                    <td class="p-3 text-right">
                        <button onclick="deletePrintSetting(${s.id})" class="text-red-500 hover:text-red-700 text-xs font-medium cursor-pointer">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        // Обработчики добавления
        function handleAddMaterial(e) {
            e.preventDefault();
            const materials = db.getMaterials();

            const newMat = {
                id: Date.now(),
                type: document.getElementById('new-mat-type').value,
                name: document.getElementById('new-mat-name').value,
                width: document.getElementById('new-mat-width').value || '-',
                amount: parseFloat(document.getElementById('new-mat-amount').value),
                unit: document.getElementById('new-mat-unit').value
            };

            materials.push(newMat);
            db.saveMaterials(materials);

            appCloseModal('modal-add-material');
            renderWarehouse();
            populateSelects();
        }

        function handleAddPrinter(e) {
            e.preventDefault();
            const printers = db.getPrinters();

            const newPrinter = {
                id: Date.now(),
                name: document.getElementById('new-printer-name').value,
                tech: document.getElementById('new-printer-tech').value,
                width: document.getElementById('new-printer-width').value || '-'
            };

            printers.push(newPrinter);
            db.savePrinters(printers);

            appCloseModal('modal-add-printer');
            renderPrinters();
            populateSelects();
        }

        function handleAddPrintSetting(e) {
            e.preventDefault();
            const settings = db.getPrintSettings();

            const newSetting = {
                id: Date.now(),
                name: document.getElementById('new-setting-name').value,
                dpi: document.getElementById('new-setting-dpi').value || '-',
                pass: document.getElementById('new-setting-pass').value || '-',
                desc: document.getElementById('new-setting-desc').value || '-'
            };

            settings.push(newSetting);
            db.savePrintSettings(settings);

            appCloseModal('modal-add-setting');
            renderPrintSettings();
            populateSelects();
        }

        // Удаление элементов
        function deleteMaterial(id) {
            let materials = db.getMaterials().filter(m => m.id !== id);
            db.saveMaterials(materials);
            renderWarehouse();
            populateSelects();
        }

        function deletePrinter(id) {
            let printers = db.getPrinters().filter(p => p.id !== id);
            db.savePrinters(printers);
            renderPrinters();
            populateSelects();
        }

        function deletePrintSetting(id) {
            let settings = db.getPrintSettings().filter(s => s.id !== id);
            db.savePrintSettings(settings);
            renderPrintSettings();
            populateSelects();
        }

        // Добавление контрагента
        function handleAddClient(e) {
            e.preventDefault();
            const clients = db.getClients();

            const newClient = {
                id: Date.now(),
                name: document.getElementById('new-client-name').value,
                phone: document.getElementById('new-client-phone').value || '-',
                email: document.getElementById('new-client-email').value || '-',
                notes: document.getElementById('new-client-notes').value || '-'
            };

            clients.push(newClient);
            db.saveClients(clients);

            appCloseModal('modal-add-client');
            renderClients();
            populateSelects();
            document.getElementById('order-client').value = newClient.name;
        }

        function renderClients() {
            const clients = db.getClients();
            const tbody = document.getElementById('clients-table-body');

            tbody.innerHTML = clients.map(c => `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold text-slate-800">${c.name}</td>
                    <td class="p-3 text-slate-600">${c.phone}</td>
                    <td class="p-3 text-slate-600">${c.email}</td>
                    <td class="p-3 text-xs text-slate-500">${c.notes}</td>
                </tr>
            `).join('');
        }

        function updateBadge() {
            const orders = db.getOrders();
            document.getElementById('active-orders-badge').innerText = orders.length;
        }
    </script>
</body>
</html>
