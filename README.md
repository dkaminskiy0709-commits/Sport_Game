    // --- МИНИ-ИГРА (100% РАБОЧАЯ) ---
    let minigameActive = false;
    let mgTimerInterval = null;
    let mgSeconds = 0;
    let mgClicks = 0;

    function openMinigameMenu() {
        // Полный сброс при открытии
        mgClicks = 0;
        mgSeconds = 0;
        minigameActive = true;
        if(mgTimerInterval) {
            clearInterval(mgTimerInterval);
            mgTimerInterval = null;
        }
        document.getElementById('mg-progress-text').innerText = '0 / 1000';
        document.getElementById('mg-timer').innerText = '⏱️ 00:00';
        document.getElementById('minigame-overlay').classList.add('active');
        
        // Навешиваем обработчик заново, чтобы не было дублей
        const area = document.getElementById('mg-click-area');
        // Удаляем старый, если есть
        const newArea = area.cloneNode(true);
        area.parentNode.replaceChild(newArea, area);
        
        newArea.addEventListener('click', function(e) {
            if(!minigameActive) return;

            // ЗАПУСК ТАЙМЕРА ПО ПЕРВОМУ КЛИКУ
            if(mgTimerInterval === null) {
                mgTimerInterval = setInterval(() => {
                    mgSeconds++;
                    let mins = String(Math.floor(mgSeconds / 60)).padStart(2, '0');
                    let secs = String(mgSeconds % 60).padStart(2, '0');
                    document.getElementById('mg-timer').innerText = `⏱️ ${mins}:${secs}`;
                }, 1000);
            }

            // Визуальный эффект (тряска)
            this.style.animation = 'shake 0.1s';
            setTimeout(() => this.style.animation = '', 100);

            // Всплывашка (цифра)
            const floatText = document.createElement('div');
            floatText.className = 'float-text';
            floatText.innerText = '+1';
            floatText.style.left = (e.offsetX - 15) + 'px';
            floatText.style.top = (e.offsetY - 15) + 'px';
            document.getElementById('mg-character-container').appendChild(floatText);
            setTimeout(() => floatText.remove(), 1000);

            // Счетчик
            mgClicks++;
            document.getElementById('mg-progress-text').innerText = `${mgClicks} / 1000`;

            // Финиш
            if(mgClicks >= 1000) {
                minigameActive = false;
                if(mgTimerInterval) {
                    clearInterval(mgTimerInterval);
                    mgTimerInterval = null;
                }
                let finalTime = document.getElementById('mg-timer').innerText.replace('⏱️ ', '');
                gameData.exp += 200;
                updateUI(); saveGame();
                alert(`🎯 1000 кликов за ${finalTime}! Получено 200 опыта.`);
                document.getElementById('minigame-overlay').classList.remove('active');
            }
        });
    }

    function closeMinigame() {
        document.getElementById('minigame-overlay').classList.remove('active');
        minigameActive = false;
        if(mgTimerInterval) {
            clearInterval(mgTimerInterval);
            mgTimerInterval = null;
        }
    }
