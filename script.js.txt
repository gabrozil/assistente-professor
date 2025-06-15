document.addEventListener('DOMContentLoaded', () => {

    const LOGO_SP_BASE64 = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAOIAAACpCAMAAACf0nJ4AAAAZlBMVEX///8AAADAwMBra2vPz8/BwcHBwcGjo6O4uLjS0tLJycno6OiwsLCnp6c3NzdjY2OmpqaioqLT09OcnJycnJzAwMDf398bGxsyMjLa2tq0tLSFhYWAQMxIAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAHj0lEQVR4nO2d63aqMBCGEZFRULUARUVrrf//n2C3tQ3BAgnP2dT3fbczmMm8mbEkAwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADgKCJds9OYp/ey/9tObezLdhq/1vP+fXsv+3Uc/P+4t5P9/a+jadbVAY/dtvV8u/Z83P3/9L6L+yFskZ/4i/1p/H4Wn49rhy2/6zYj5K6F/3U/aev2+WpuRkgr/+q9t5k/n4/TdQPy/Spv5L9c1+3zVV4GSCr/+l2iH7e6HbZ2A1K2/tU7bPFy/b7N1xFIxv/qPTZ/3u522BqKkJ7/1btk/Lzd7bA1HCHB/6p2C/e7HbZGKyFYP3Wb1b8/H252WBoqkND4q3fI+PnDdbfA1nCEBMan3SXu1vT7BYYmJGT81buE/X7c6OBTpCkEZP7Vu8QzfnP5oFOkKcIRcbN6FUl8frnBo0hThCPiZ/Umkf59vcGjSFMEM+Jl9S6T7s/rDR5GmkDsEFuvNxrJfr3Z40jTCD2h6XqHkf98scHjSFMIHWLatfX+e2D014f3y+b8h8xP4v9k61GGP/4n+/Y0YgFee7+w2rN8f/PZH9COp/APLftzhnjfyzknxG6QW4K++zWGOO/zOYnQmsoXmD77daaM/7/JnuQ0AiqB7h1j/X5+yfZo4RC/IPNN9tCjf8/knyWkIjCD2y/1Rrj/5/MHkVoQ2EG9N9t1s/5/0eyL2GMwDfsV18+4/832Y8lDOGL9mtv/pr/X2SfQBiFv+6fK+f9n2QfIozCX23Xk/K+n+SfYwwReHv/rZXy/s/yk0Jjx/Xm/v+k1lZ5zfcvMgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADibv4Ll+xIq73o/HAAAAAASUVORK5CYII=';
    const SUBJECTS = ['Português', 'Matemática', 'História', 'Geografia', 'Ciências', 'Física', 'Biologia', 'Geopolítica', 'Artes', 'Inglês', 'Filosofia', 'Sociologia', 'Projeto de Vida', 'Oratória', 'Liderança'];
    const GRADE_LEVELS = ['Ensino Fundamental 1', 'Ensino Fundamental 2', 'Ensino Médio'];
    const QUESTION_STYLES = ['Alternativas', 'Misto', 'Dissertativas'];
    const DIFFICULTY_LEVELS = ['Fácil', 'Normal', 'Médio', 'Difícil'];
    const NUM_QUESTIONS_OPTIONS = ['1','2','3','4','5','6','7','8','9','10', 'Outro'];
    const EXAMPLE_TOPICS = {
        'História-Normal': 'Revolução Francesa', 'História-Difícil': 'Crise do Império Romano',
        'Matemática-Fácil': 'Operações com Frações', 'Matemática-Médio': 'Teorema de Pitágoras', 'Matemática-Difícil': 'Análise Combinatória',
        'Default': 'Selecione a matéria e a dificuldade'
    };
    
    const dom = {
        pages: {
            carousel: document.getElementById('carousel-section'),
            assistant: document.getElementById('assistant-page-container'),
            brand: document.getElementById('brand-page-container'),
        },
        modals: {
            payment: document.getElementById('payment-modal'),
            alert: document.getElementById('alert-modal'),
        },
        carousel: {
            wrapper: document.querySelector('.carousel-wrapper'),
            slides: document.querySelectorAll('.carousel-slide'),
            prevBtn: document.querySelector('.carousel-nav.prev'),
            nextBtn: document.querySelector('.carousel-nav.next'),
        },
        form: {
            schoolName: document.getElementById('school-name'),
            profName: document.getElementById('prof-name'),
            studentName: document.getElementById('student-name'),
            studentSeries: document.getElementById('student-series'),
            topic: document.getElementById('topic'),
            customNumQuestions: document.getElementById('custom-num-questions'),
        },
        buttons: {
            startAssistant: document.getElementById('start-assistant-btn'),
            learnMore: document.getElementById('learn-more-btn'),
            payment: document.getElementById('payment-btn'),
            back: document.querySelectorAll('.back-button'),
            closePaymentModal: document.getElementById('close-payment-modal-btn'),
            closeAlertModal: document.getElementById('close-alert-modal-btn'),
            generate: document.getElementById('generate-btn'),
            copy: document.getElementById('copy-btn'),
            pdf: document.getElementById('pdf-btn'),
            word: document.getElementById('word-btn'),
            gabarito: document.getElementById('gabarito-btn'),
        },
        containers: {
            subject: document.getElementById('subject-buttons-container'),
            gradeLevel: document.getElementById('grade-level-buttons-container'),
            questionStyle: document.getElementById('question-style-buttons-container'),
            numQuestions: document.getElementById('num-questions-buttons-container'),
            difficulty: document.getElementById('difficulty-buttons-container'),
            loading: document.getElementById('loading-container'),
            result: document.getElementById('result-container'),
            gabarito: document.getElementById('gabarito-container'),
        },
        payment: {
            qrCodeImage: document.getElementById('payment-qrcode-image'),
        },
        outputs: {
            timer: document.getElementById('timer'),
            activity: document.getElementById('activity-output'),
            gabarito: document.getElementById('gabarito-output'),
            alertMessage: document.getElementById('alert-message'),
        },
        canvas: document.getElementById('generate-btn-canvas'),
    };

    const state = {
        currentScrollIndex: 0,
        generatedGabarito: '',
        selection: {
            subject: '',
            gradeLevel: 'Ensino Médio',
            questionStyle: 'Alternativas',
            difficulty: 'Normal',
            numQuestions: 5
        }
    };
    
    // --- Funções de Navegação e UI ---

    const showPage = (pageId) => {
        Object.values(dom.pages).forEach(page => page.classList.add('hidden'));
        const targetPage = dom.pages[pageId];
        if (targetPage) {
            targetPage.classList.remove('hidden');
        }
    };

    const showAlert = (message) => {
        dom.outputs.alertMessage.textContent = message;
        dom.modals.alert.style.display = 'flex';
    };
    
    const updateCarousel = () => {
        const getVisibleSlides = () => (window.innerWidth <= 600) ? 1 : (window.innerWidth <= 900) ? 2 : 3;
        const visibleSlides = getVisibleSlides();
        const totalSlides = dom.carousel.slides.length;
        const scrollableSlides = totalSlides - visibleSlides;
        state.currentScrollIndex = Math.max(0, Math.min(state.currentScrollIndex, scrollableSlides));
        if (totalSlides > 0) {
            const slideWidth = dom.carousel.slides[0].getBoundingClientRect().width;
            const offset = -state.currentScrollIndex * (slideWidth + 20);
            dom.carousel.wrapper.style.transform = `translateX(${offset}px)`;
        }
        dom.carousel.prevBtn.disabled = state.currentScrollIndex === 0;
        dom.carousel.nextBtn.disabled = state.currentScrollIndex >= scrollableSlides;
    };
    
    const createOptionButtons = (container, items, defaultActive) => {
        const key = container.dataset.selectionKey;
        if(defaultActive) {
            state.selection[key] = defaultActive;
        } else {
            state.selection[key] = '';
        }
        items.forEach(item => {
            const button = document.createElement('button');
            button.type = 'button';
            button.className = `clickable-btn ${item === defaultActive ? 'active' : ''}`;
            button.textContent = item;
            button.dataset.value = item;
            container.appendChild(button);
        });
    };

    const updateTopicPlaceholder = () => {
        const { subject, difficulty } = state.selection;
        const key = `${subject}-${difficulty}`;
        const placeholder = EXAMPLE_TOPICS[key] || `Ex: Tema de ${subject || 'uma matéria'}`;
        dom.form.topic.placeholder = placeholder;
    };
    
    const handleOptionClick = (e) => {
        const button = e.target.closest('.clickable-btn');
        if (!button) return;

        const container = button.parentElement;
        const key = container.dataset.selectionKey;
        const value = button.dataset.value;

        container.querySelectorAll('.clickable-btn').forEach(btn => btn.classList.remove('active'));
        button.classList.add('active');
        
        if (key === 'numQuestions') {
            dom.form.customNumQuestions.classList.add('hidden');
            if (value === 'Outro') {
                dom.form.customNumQuestions.classList.remove('hidden');
                dom.form.customNumQuestions.focus();
                state.selection.numQuestions = parseInt(dom.form.customNumQuestions.value, 10) || 11;
            } else {
                state.selection.numQuestions = parseInt(value, 10);
            }
        } else {
            state.selection[key] = value;
        }

        updateTopicPlaceholder();
        saveState();
    };
    
    function handlePaymentClick() {
        const qrCodeImageUrl = 'https://i.ibb.co/WZ00gpK/ima-PNG.jpg';
        dom.payment.qrCodeImage.src = qrCodeImageUrl;
        dom.modals.payment.style.display = 'flex';
    }

    // --- Funções de Geração de Conteúdo ---
    
    const getHeaderData = () => ({
        school: dom.form.schoolName.value,
        prof: dom.form.profName.value,
        student: dom.form.studentName.value,
        series: dom.form.studentSeries.value,
    });

    const getPrompt = () => {
        const { topic } = dom.form;
        const { subject, gradeLevel, difficulty, questionStyle, numQuestions } = state.selection;
        
        let enemInstruction = '';
        if (gradeLevel === 'Ensino Médio') {
            enemInstruction = "Se o nível for 'Ensino Médio', formate TODAS as questões no estilo ENEM: inclua um texto base (uma citação, um pequeno artigo, um gráfico, etc.) antes do enunciado da pergunta.";
        }

        let styleInstructionText = '';
        switch(questionStyle) {
            case 'Alternativas':
                styleInstructionText = `${numQuestions} perguntas de múltipla escolha (com 4 alternativas cada, de A a D).`;
                break;
            case 'Dissertativas':
                styleInstructionText = `${numQuestions} perguntas dissertativas, cada uma seguida por 5 linhas de resposta para o aluno.`;
                break;
            case 'Misto':
                const dissertativasCount = Math.floor(numQuestions / 2);
                const multiplaCount = numQuestions - dissertativasCount;
                styleInstructionText = `${dissertativasCount} perguntas dissertativas (com 5 linhas de resposta para o aluno cada) e ${multiplaCount} de múltipla escolha.`;
                break;
        }

        return `Gere um trabalho sobre "${topic.value}" para a matéria de "${subject}" do nível "${gradeLevel}", com dificuldade "${difficulty}".
Regras estritas:
1. Crie exatamente o seguinte: ${styleInstructionText}
2. ${enemInstruction}
3. Para questões de múltipla escolha, diversifique as letras corretas.
4. Gere TODAS as questões primeiro, em sequência.
5. Após a última questão, insira a tag de separação: [GABARITO]
6. Após a tag [GABARITO], escreva o gabarito em uma lista numerada, onde cada número corresponde a uma questão (ex: "1. Resposta da questão 1", "2. Resposta da questão 2", etc.).
7. A saída deve ser em texto puro, sem usar markdown (como **, *, #).`;
    };
    
    async function generateWorkRequest(prompt) {
        // Esta função agora simula a chamada para um back-end.
        // Em um projeto real, você faria um fetch para seu próprio servidor.
        console.log("Enviando requisição para o back-end com o prompt:", prompt);
        
        const response = await fetch('/.netlify/functions/generate', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ prompt: prompt })
        });
        
        if (!response.ok) {
            const errorData = await response.json();
            throw new Error(errorData.error || 'Erro desconhecido no servidor.');
        }
        
        const result = await response.json();
        return result.generatedText;
    }

    async function handleGenerateClick() {
        const missing = [];
        if (!state.selection.subject) missing.push('Matéria');
        if (!dom.form.topic.value) missing.push('Tema');

        if (missing.length > 0) {
            showAlert(`Por favor, preencha os seguintes campos: ${missing.join(', ')}.`);
            return;
        }

        dom.containers.loading.classList.remove('hidden');
        dom.containers.result.classList.add('hidden');
        dom.containers.gabarito.classList.add('hidden');
        dom.buttons.gabarito.classList.add('hidden');
        dom.buttons.generate.disabled = true;
        dom.buttons.generate.querySelector('.btn-text').textContent = 'Elaborando...';

        let seconds = 0;
        const timerInterval = setInterval(() => {
            seconds++;
            dom.outputs.timer.textContent = seconds;
        }, 1000);

        try {
            const prompt = getPrompt();
            const rawText = await generateWorkRequest(prompt);
            const parts = rawText.split(/\[GABARITO\]/i);
            const questions = parts[0] ? parts[0].trim() : "Não foi possível gerar as questões.";
            state.generatedGabarito = parts[1] ? parts[1].trim() : "Não foi possível gerar o gabarito.";

            dom.outputs.activity.textContent = questions;
            dom.containers.result.classList.remove('hidden');
            if (state.generatedGabarito) {
                 dom.buttons.gabarito.classList.remove('hidden');
            }
        } catch (error) {
            console.error("Erro ao gerar trabalho:", error);
            showAlert(`Erro ao gerar trabalho: ${error.message}`);
        } finally {
            clearInterval(timerInterval);
            dom.containers.loading.classList.add('hidden');
            dom.buttons.generate.disabled = false;
            dom.buttons.generate.querySelector('.btn-text').innerHTML = '✨ Gerar Trabalho Novamente';
        }
    }

    // --- Lógica de Persistência (localStorage) ---
    function saveState() {
        const stateToSave = {
            selection: state.selection,
            formValues: {
                schoolName: dom.form.schoolName.value,
                profName: dom.form.profName.value,
                studentName: dom.form.studentName.value,
                studentSeries: dom.form.studentSeries.value,
                topic: dom.form.topic.value,
            }
        };
        localStorage.setItem('teacherAssistantState', JSON.stringify(stateToSave));
    }

    function loadState() {
        const savedState = localStorage.getItem('teacherAssistantState');
        if (savedState) {
            const parsedState = JSON.parse(savedState);
            state.selection = { ...state.selection, ...parsedState.selection };
            
            if (parsedState.formValues) {
                Object.keys(parsedState.formValues).forEach(key => {
                    if (dom.form[key]) {
                        dom.form[key].value = parsedState.formValues[key];
                    }
                });
            }
            applyStateToUI();
        }
    }
    
    function applyStateToUI() {
        for (const key in state.selection) {
            const container = dom.containers[key];
            if (container) {
                container.querySelectorAll('.clickable-btn').forEach(btn => {
                    btn.classList.toggle('active', btn.dataset.value === String(state.selection[key]));
                });
            }
        }
        updateTopicPlaceholder();
    }

    // --- Efeito de Partículas ---
    const canvas = dom.canvas;
    const ctx = canvas.getContext('2d');
    let particles = [];

    function resizeCanvas() {
        canvas.width = dom.buttons.generate.offsetWidth;
        canvas.height = dom.buttons.generate.offsetHeight;
    }

    class Particle {
        constructor(x, y) {
            this.x = x;
            this.y = y;
            this.radius = Math.random() * 2.5 + 1;
            this.color = `rgba(220, 220, 220, 1)`;
            this.alpha = 1;
            this.dx = (Math.random() - 0.5) * 2;
            this.dy = (Math.random() - 0.5) * 2;
        }
        update() {
            this.x += this.dx;
            this.y += this.dy;
            this.alpha -= 0.03;
        }
        draw() {
            ctx.save();
            ctx.globalAlpha = this.alpha;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, false);
            ctx.fillStyle = this.color;
            ctx.fill();
            ctx.restore();
        }
    }

    function animateParticles() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        particles.forEach((p, i) => {
            p.update();
            p.draw();
            if (p.alpha <= 0) {
                particles.splice(i, 1);
            }
        });
        requestAnimationFrame(animateParticles);
    }
    
    // --- Função Principal de Inicialização ---
    const initialize = () => {
        // Navegação entre páginas
        dom.buttons.startAssistant.addEventListener('click', () => showPage('assistant'));
        dom.buttons.learnMore.addEventListener('click', () => showPage('brand'));
        dom.buttons.back.forEach(btn => btn.addEventListener('click', (e) => {
            const targetPageId = e.currentTarget.dataset.targetPage.replace('-section', '');
            showPage(targetPageId);
        }));
        
        // Modais
        dom.buttons.payment.addEventListener('click', handlePaymentClick);
        dom.buttons.closePaymentModal.addEventListener('click', () => dom.modals.payment.style.display = 'none');
        dom.buttons.closeAlertModal.addEventListener('click', () => dom.modals.alert.style.display = 'none');
        window.addEventListener('click', (e) => {
            if (e.target === dom.modals.payment) dom.modals.payment.style.display = 'none';
            if (e.target === dom.modals.alert) dom.modals.alert.style.display = 'none';
        });
        
        // Carrossel
        dom.carousel.nextBtn.addEventListener('click', () => { state.currentScrollIndex++; updateCarousel(); });
        dom.carousel.prevBtn.addEventListener('click', () => { state.currentScrollIndex--; updateCarousel(); });
        window.addEventListener('resize', updateCarousel);
        
        // Criação dos Botões de Opção do Formulário
        createOptionButtons(dom.containers.subject, SUBJECTS);
        createOptionButtons(dom.containers.gradeLevel, GRADE_LEVELS, 'Ensino Médio');
        createOptionButtons(dom.containers.questionStyle, QUESTION_STYLES, 'Alternativas');
        createOptionButtons(dom.containers.difficulty, DIFFICULTY_LEVELS, 'Normal');
        createOptionButtons(dom.containers.numQuestions, NUM_QUESTIONS_OPTIONS, '5');
        
        // Listeners para cliques nos botões de opção
        Object.values(dom.containers).forEach(container => {
            if (container.classList.contains('clickable-buttons')) {
                container.addEventListener('click', handleOptionClick);
            }
        });
        
        // Listeners para inputs que precisam salvar estado
        dom.form.customNumQuestions.addEventListener('input', (e) => {
            let value = parseInt(e.target.value, 10);
            if (value > 30) {
                showAlert('O número máximo permitido é 30.');
                e.target.value = 30;
                value = 30;
            }
            state.selection.numQuestions = (!isNaN(value) && value > 0) ? value : state.selection.numQuestions;
            saveState();
        });

        Object.values(dom.form).forEach(input => input.addEventListener('input', saveState));

        // Botões de ação
        dom.buttons.generate.addEventListener('click', handleGenerateClick);
        dom.buttons.gabarito.addEventListener('click', parseAndDisplayGabarito);
        
        dom.buttons.copy.addEventListener('click', () => {
            const originalHtml = dom.buttons.copy.innerHTML;
            navigator.clipboard.writeText(dom.outputs.activity.textContent).then(() => {
                dom.buttons.copy.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16"><path d="M12.736 3.97a.733.733 0 0 1 1.047 0c.286.289.29.756.01 1.05L7.88 12.01a.733.733 0 0 1-1.065.02L3.217 8.384a.757.757 0 0 1 0-1.06.733.733 0 0 1 1.047 0l3.052 3.093 5.4-6.425z"/></svg> Copiado!';
                setTimeout(() => { dom.buttons.copy.innerHTML = originalHtml; }, 2000);
            }).catch(err => {
                console.error('Erro ao copiar texto: ', err);
                showAlert('Não foi possível copiar o texto.');
            });
        });
        
        // Inicializa o canvas de partículas
        resizeCanvas();
        animateParticles();
        window.addEventListener('resize', resizeCanvas);
        dom.buttons.generate.addEventListener('mouseenter', (e) => {
            const rect = e.target.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            for (let i = 0; i < 30; i++) { 
                particles.push(new Particle(x, y));
            }
        });

        // Carrega o estado salvo no início
        loadState();
        updateCarousel();
    };

    initialize();
});
