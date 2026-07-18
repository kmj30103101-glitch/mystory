# mystory<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>너구리 점프 게임</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: Arial, sans-serif;
        }
        #score-board {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 10px;
        }
        #game-view {
            width: 600px;
            height: 200px;
            border-bottom: 4px solid #333;
            position: relative;
            overflow: hidden;
            background-color: #fff;
        }
        /* 너구리 (갈색 네모) */
        #raccoon {
            width: 40px;
            height: 40px;
            background-color: #8B4513;
            border-radius: 8px;
            position: absolute;
            bottom: 0;
            left: 50px;
        }
        /* 점프 애니메이션 효과 */
        .jump {
            animation: jumpAnimation 0.6s linear;
        }
        @keyframes jumpAnimation {
            0% { bottom: 0; }
            50% { bottom: 100px; }
            100% { bottom: 0; }
        }
        /* 가시 (빨간 삼각형) */
        #spike {
            width: 0;
            height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-bottom: 30px solid #ff4d4d;
            position: absolute;
            bottom: 0;
            right: -30px;
        }
        /* 가시가 왼쪽으로 달려오는 애니메이션 */
        .obstacle-move {
            animation: moveSpike 2s infinite linear;
        }
        @keyframes moveSpike {
            0% { right: -30px; }
            100% { right: 630px; }
        }
        #game-over-text {
            display: none;
            color: red;
            font-size: 30px;
            margin-top: 20px;
        }
    </style>
</head>
<body>

    <div id="score-board">점수: <span id="score">0</span></div>
    <div id="game-view">
        <div id="raccoon"></div>
        <div id="spike" class="obstacle-move"></div>
    </div>
    <div id="game-over-text">게임 오버! (F5를 눌러 재시작)</div>

    <script>
        const raccoon = document.getElementById('raccoon');
        const spike = document.getElementById('spike');
        const scoreDisplay = document.getElementById('score');
        const gameOverText = document.getElementById('game-over-text');

        let score = 0;
        let isJumping = false;
        let isGameOver = false;
        let passedSpike = false; // 이번 가시를 이미 넘었는지 체크

        // 1. 스페이스바 누르면 점프
        document.addEventListener('keydown', function(event) {
            if (event.code === 'Space' && !isJumping && !isGameOver) {
                isJumping = true;
                raccoon.classList.add('jump');

                // 0.6초(애니메이션 시간) 후에 점프 상태 해제
                setTimeout(() => {
                    raccoon.classList.remove('jump');
                    isJumping = false;
                }, 600);
            }
        });

        // 2. 충돌 감지 및 점수 계산 루프 (10ms마다 확인)
        const gameLoop = setInterval(() => {
            if (isGameOver) return;

            // 너구리와 가시의 현재 위치 계산
            const raccoonBottom = parseInt(window.getComputedStyle(raccoon).getPropertyValue('bottom'));
            const spikeLeft = spike.getBoundingClientRect().left;
            const raccoonLeft = raccoon.getBoundingClientRect().left;
            const raccoonRight = raccoonLeft + 40;

            // 충돌 조건 (가시가 너구리 범위 안에 있고, 너구리 높이가 낮을 때)
            if (spikeLeft > raccoonLeft && spikeLeft < raccoonRight && raccoonBottom <= 30) {
                isGameOver = true;
                spike.classList.remove('obstacle-move'); // 가시 멈춤
                gameOverText.style.display = 'block';
                clearInterval(gameLoop);
            }

            // 점수 획득 조건: 가시를 안전하게 지나쳤을 때
            if (spikeLeft < raccoonLeft && !passedSpike) {
                score += 10;
                scoreDisplay.textContent = score;
                passedSpike = true; // 점수 중복 추가 방지
            }

            // 가시가 화면 왼쪽 끝으로 사라져서 초기화될 때 체크 해제
            if (spikeLeft > raccoonRight + 100) {
                passedSpike = false;
            }
        }, 10);
    </script>
</body>
</html>
