function render() {
    const container = document.getElementById('food-list-container');
    container.innerHTML = '';
    const calendarEvents = [];
    const today = new Date();

    foods.forEach(food => {
        const entry = new Date(food.entryDate);
        const expire = new Date(food.expireDate);
        const totalDuration = expire - entry;
        const elapsed = today - entry;
        
        // Calculate percentage of "freshness" left
        let percentLeft = Math.max(0, Math.min(100, 100 - (elapsed / totalDuration * 100)));
        const daysLeft = Math.ceil((expire - today) / (1000 * 60 * 60 * 24));

        let statusClass = daysLeft < 0 ? 'status-expired' : (daysLeft <= 2 ? 'status-warning' : 'status-ok');
        let barColor = daysLeft < 0 ? 'var(--danger)' : (daysLeft <= 2 ? 'var(--warning)' : 'var(--success)');

        container.innerHTML += `
            <div class="food-item ${statusClass}">
                <button class="delete-btn" onclick="deleteFood(${food.id})">✖</button>
                <span class="badge ${food.mode === 'fridge' ? 'badge-fridge' : 'badge-freezer'}">
                    ${food.mode === 'fridge' ? '❄️ ช่องธรรมดา' : '🧊 ช่องแช่แข็ง'}
                </span>
                <div style="font-weight:bold; font-size: 1.2em;">${food.name}</div>
                
                <div class="progress-container">
                    <div class="progress-bar" style="width: ${percentLeft}%; background-color: ${barColor};"></div>
                </div>

                <div style="font-size: 0.85em; color: #666;">
                    เหลือ: ${daysLeft < 0 ? 'หมดอายุแล้ว' : daysLeft + ' วัน'}
                </div>
            </div>
        `;

        calendarEvents.push({
            title: food.name,
            start: food.expireDate,
            backgroundColor: barColor
        });
    });

    calendar.removeAllEvents();
    calendar.addEventSource(calendarEvents);
}
