import streamlit as st
import random
import re
import time

# --- Streamlit Title and Page Config ---
# 페이지 레이아웃을 넓게 설정하고 제목을 지정합니다.
st.set_page_config(
    page_title="Sinu Teacher's Fun English Time",
    layout="wide",
    initial_sidebar_state="collapsed"
)

# 사용자 정의 CSS GEMINI_API_KEY = "당신의-구글-AI-API-키"(디자인 개선용)
st.markdown("""
<style>
/* Streamlit 메인 콘텐츠 영역 */
.stApp {
    padding-top: 10px;
    padding-bottom: 10px;
    background: #f5f5f5;
}

/* 채팅 기록 컨테이너 - 카톡 스타일 */
.stContainer {
    border-radius: 20px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    background: white;
    padding: 16px;
}

/* 제목 스타일 */
h1 {
    background: linear-gradient(120deg, #6366f1 0%, #8b5cf6 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    border-bottom: 3px solid #6366f1;
    padding-bottom: 10px;
    font-weight: 800;
}

/* 버튼 스타일 개선 - 카톡 메시지 버튼 */
.stButton>button {
    border-radius: 20px;
    border: none;
    font-weight: 600;
    transition: all 0.2s ease;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white !important;
    padding: 12px 24px;
    box-shadow: 0 4px 12px rgba(102, 126, 234, 0.3);
}

.stButton>button:hover {
    background: linear-gradient(135deg, #5a67d8 0%, #6a3f95 100%) !important;
    transform: translateY(-2px);
    box-shadow: 0 6px 16px rgba(102, 126, 234, 0.5) !important;
}

/* 채팅 메시지 박스 - 카톡 메시지 스타일 */
.stChatMessage {
    border-radius: 16px;
    padding: 12px;
    background-color: transparent;
}

.stChatMessage [data-testid="chatAvatarIcon"] {
    font-size: 28px;
}

/* 모델 메시지 (Sinu) - 왼쪽 정렬 */
[data-testid="chatMessageContainer"]:has([data-testid="chatAvatarIcon"]:contains("⭐")) {
    margin-right: auto;
}

/* 사용자 메시지 - 오른쪽 정렬 */
[data-testid="chatMessageContainer"]:has([data-testid="chatAvatarIcon"]:contains("🧑")) {
    margin-left: auto;
}

/* subheader 스타일 */
h2, h3 {
    color: #333333;
    font-weight: 700;
}

/* markdown 텍스트 */
p {
    font-size: 16px;
    line-height: 1.6;
    color: #333333;
}

/* 구분선 */
hr {
    border: none;
    height: 1px;
    background-color: #e0e0e0;
    margin: 16px 0;
}

/* 채팅 입력창 스타일 */
.stTextInput>div>div>input {
    border-radius: 20px;
    border: 2px solid #e0e0e0;
    padding: 12px 16px;
}

.stTextInput>div>div>input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

/* 스크롤 대상 */
.scroll-target {
    scroll-margin-top: 20px;
}
</style>

<script>
// 페이지 로드 시 하단으로 스크롤
window.addEventListener('load', function() {
    var target = document.querySelector('.scroll-target');
    if (target) {
        target.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
});

// Streamlit 재렌더링 감지 후 스크롤
function watchForChanges() {
    var target = document.querySelector('.scroll-target');
    if (target) {
        setTimeout(function() {
            target.scrollIntoView({ behavior: 'smooth', block: 'start' });
        }, 100);
    }
}

// 페이지 업데이트 감지
const observer = new MutationObserver(function(mutations) {
    watchForChanges();
});

observer.observe(document.body, { childList: true, subtree: true });
</script>
""", unsafe_allow_html=True)

st.title("⭐ Sinu Teacher's Fun English Time")
st.markdown(
    """
    ### 📚 "What is your favorite subject?" 차시
    
    오늘 배운 **"What is your favorite subject?"** 차시에 대한 수업 내용을 챗봇과 대화하며 문제를 풀어봅시다! 🎯
    
    다양한 유형의 문제들을 풀면서 영어 표현을 더욱 완벽하게 익혀보세요! 
   
    """
)

# --- 고정 데이터 및 상수 ---
SUBJECTS = ["Math", "Science", "P.E.", "Art", "Music", "English", "History", "Korean"]
KOR_SUBJECTS = {"Math": "수학", "Science": "과학", "P.E.": "체육", "Art": "미술", "Music": "음악", "English": "영어", "History": "역사", "Korean": "국어"}
PHRASES = {
    "korean_to_english": "이 과목의 영어 이름은 무엇일까요? **{kor}**",
    "english_to_korean": "이 과목의 한국어 이름은 무엇일까요? **{eng}**",
    "q_pattern": "좋아하는 과목을 묻는 영어 표현은?",
    "a_pattern": "'나는 {kor}을/를 좋아해' 영어 표현은?"
}
QUIZ_QUESTIONS = [
    # 1. Subject KOR -> ENG (수학)
    {"type": "subject_k2e", "kor": "수학", "eng": "Math", "options": ["Science", "Art"]},
    # 2. Subject ENG -> KOR (체육)
    {"type": "subject_e2k", "eng": "P.E.", "kor": "체육", "options": ["미술", "음악"]},
    # 3. Situation (상황 판단 - 대화 상황)
    {"type": "situation", "scenario": "친구: \"I like drawing and painting.\" 친구가 무엇을 좋아할까요?", "answer": "Art", "options": ["P.E.", "Music"]},
    # 4. True/False (참/거짓)
    {"type": "true_false", "statement": "'I like Art'는 '나는 미술을 좋아해'라는 뜻이다.", "answer": True},
    # 5. Question Pattern
    {"type": "q_pattern", "q_kor": PHRASES["q_pattern"], "eng": "What is your favorite subject?", "options": ["What subject do you like?", "What is your name?"]},
    # 6. Answer Pattern (미술)
    {"type": "a_pattern", "kor": "미술", "eng": "My favorite subject is Art.", "options": ["I like P.E.", "I am sleepy."]},
    # 7. Subject KOR -> ENG (역사)
    {"type": "subject_k2e", "kor": "역사", "eng": "History", "options": ["Music", "English"]},
    # 8. Situation (상황 판단 - 대화 상황)
    {"type": "situation", "scenario": "학생: \"I enjoy learning about numbers and solving problems.\" 이 학생이 좋아하는 과목은?", "answer": "Math", "options": ["Science", "Korean"]},
    # 9. True/False (참/거짓)
    {"type": "true_false", "statement": "'What is your favorite subject?'는 좋아하는 과목을 묻는 표현이다.", "answer": True},
    # 10. Subject ENG -> KOR (영어)
    {"type": "subject_e2k", "eng": "English", "kor": "영어", "options": ["국어", "과학"]},
    # 11. Situation (상황 판단 - 왜 그럴까?)
    {"type": "situation_why", "scenario": "학생이 \"My favorite subject is Music.\"이라고 했습니다. 왜 음악을 좋아할까요?", "answer": "I enjoy singing and playing instruments.", "options": ["I like running and sports.", "I like reading books."]},
    # 12. Answer Pattern (음악)
    {"type": "a_pattern", "kor": "음악", "eng": "I like Music.", "options": ["My favorite is Science.", "It is boring."]}
]


# --- Streamlit 상태 관리 초기화 및 리셋 로직 ---
def clear_session():
    """모든 세션 상태를 초기값으로 재설정합니다."""
    # 퀴즈 데이터 순서 고정 (random.shuffle 제거)
    st.session_state.quiz_data = QUIZ_QUESTIONS.copy()
    
    st.session_state.history = []
    st.session_state.current_q_index = -1 # -1은 시작 전 상태를 의미
    st.session_state.score = {"correct": 0, "total": 0}
    st.session_state.is_finished = False
    st.session_state.options = []
    st.session_state.correct_answer = ""
    st.session_state.is_report_shown = False 

# 최초 로드 시 상태 초기화
if "current_q_index" not in st.session_state or st.session_state.current_q_index == -1:
    clear_session()


# --- 챗봇 코어 로직 ---

def generate_question():
    """다음 퀴즈 질문을 생성하고 상태를 업데이트합니다."""
    
    q_index = st.session_state.current_q_index
    q_data = st.session_state.quiz_data[q_index]
    
    # 질문 텍스트 생성 및 정답 설정
    if q_data["type"] == "subject_k2e":
        question = PHRASES["korean_to_english"].format(kor=q_data["kor"])
        options = q_data["options"] + [q_data["eng"]]
        correct_answer = q_data["eng"]
    elif q_data["type"] == "subject_e2k":
        question = PHRASES["english_to_korean"].format(eng=q_data["eng"])
        options = q_data["options"] + [q_data["kor"]]
        correct_answer = q_data["kor"]
    elif q_data["type"] == "q_pattern":
        question = q_data["q_kor"]
        options = q_data["options"] + [q_data["eng"]]
        correct_answer = q_data["eng"]
    elif q_data["type"] == "a_pattern":
        question = PHRASES["a_pattern"].format(kor=q_data["kor"])
        options = q_data["options"] + [q_data["eng"]]
        correct_answer = q_data["eng"]
    elif q_data["type"] == "situation":
        question = f"🎭 **만약 이런 상황이라면?**\n\n{q_data['scenario']}"
        options = q_data["options"] + [q_data["answer"]]
        correct_answer = q_data["answer"]
    elif q_data["type"] == "situation_why":
        question = f"❓ **왜 그럴까?**\n\n{q_data['scenario']}"
        options = q_data["options"] + [q_data["answer"]]
        correct_answer = q_data["answer"]
    elif q_data["type"] == "true_false":
        question = f"⭕ 참/거짓: **{q_data['statement']}**"
        options = ["✅ 참 (O)", "❌ 거짓 (X)"]
        correct_answer = "✅ 참 (O)" if q_data["answer"] else "❌ 거짓 (X)"
        random.shuffle(options)
        st.session_state.correct_answer = correct_answer
        st.session_state.options = options
        return f"**Sinu** | {question}"
    else:
        # 기타 타입
        question = "문제를 불러올 수 없습니다."
        options = []
        correct_answer = ""
    
    random.shuffle(options)
    
    st.session_state.correct_answer = correct_answer
    st.session_state.options = options
    
    return f"**Sinu** | {question}"

def generate_next_question_and_update_history():
    """다음 질문을 생성하고 history에 추가합니다."""
    
    if st.session_state.current_q_index < len(st.session_state.quiz_data):
        new_question = generate_question()
        st.session_state.history.append({"role": "model", "text": new_question})
    else:
        st.session_state.is_finished = True


def handle_answer(selected_option):
    """사용자의 답변을 처리하고 다음 단계로 이동합니다."""
    
    is_correct = selected_option == st.session_state.correct_answer
    
    # 1. 채점 및 기록
    st.session_state.score["total"] += 1
    if is_correct:
        st.session_state.score["correct"] += 1
        feedback = "✅ **정답입니다!** 정말 잘했어요! 🎉✨"
    else:
        feedback = f"❌ **아쉽지만 틀렸어요!** 😢\n\n**정답:** '{st.session_state.correct_answer}'\n\n다음 문제를 풀어보세요!"
        
    st.session_state.history.append({"role": "user", "text": selected_option})
    st.session_state.history.append({"role": "model", "text": feedback})

    # 2. 다음 질문 인덱스 준비
    st.session_state.current_q_index += 1
    
    # 3. 종료 확인 및 다음 질문 생성 (리렌더링 전에 다음 상태를 확정)
    if st.session_state.current_q_index < len(st.session_state.quiz_data):
        generate_next_question_and_update_history()
    else:
        st.session_state.is_finished = True
    
    # 4. 리렌더링 유도
    st.rerun()


# --- UI 랜더링 함수 ---

def render_final_report_page():
    """학습 완료 보고서를 시각적으로 보여줍니다."""
    
    score = st.session_state.score
    total_questions = len(st.session_state.quiz_data)
    correct_answers = score["correct"]
    quiz_percent = (correct_answers / total_questions) * 100 if total_questions > 0 else 0
    
    st.header("🎉 학습 완료 보고서!")
    st.markdown("---")

    # 1. 시각화 (그래프/표 역할)
    
    col1, col2, col3 = st.columns(3)

    with col1:
        st.markdown(
            f"""
            <div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); border-radius: 15px; padding: 20px; text-align: center; box-shadow: 0 8px 16px rgba(102, 126, 234, 0.4); color: white;">
                <h3 style="font-size: 1.1rem; margin: 0; font-weight: 700;">📚 최종 정답률</h3>
                <p style="font-size: 2.5rem; font-weight: bold; margin: 10px 0;">{correct_answers}/{total_questions}</p>
                <div style="width: 100%; height: 8px; background-color: rgba(255,255,255,0.3); border-radius: 10px; overflow: hidden; margin-top: 10px;">
                    <div style="height: 100%; width: {quiz_percent}%; background-color: #4ade80;"></div>
                </div>
                <p style="font-size: 0.9rem; margin: 8px 0 0 0;">{quiz_percent:.0f}%</p>
            </div>
            """, 
            unsafe_allow_html=True
        )

    with col2:
        st.markdown(
            f"""
            <div style="background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%); border-radius: 15px; padding: 20px; text-align: center; box-shadow: 0 8px 16px rgba(245, 87, 108, 0.4); color: white;">
                <h3 style="font-size: 1.1rem; margin: 0; font-weight: 700;">🎯 정답 개수</h3>
                <p style="font-size: 2.5rem; font-weight: bold; margin: 10px 0;">{correct_answers}</p>
                <p style="font-size: 0.9rem; margin: 8px 0 0 0;">{'완벽합니다! 🥳' if quiz_percent == 100 else '좋은 결과입니다! 💪'}</p>
            </div>
            """, 
            unsafe_allow_html=True
        )

    with col3:
        st.markdown(
            f"""
            <div style="background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%); border-radius: 15px; padding: 20px; text-align: center; box-shadow: 0 8px 16px rgba(79, 172, 254, 0.4); color: white;">
                <h3 style="font-size: 1.1rem; margin: 0; font-weight: 700;">⭐ 학습 평가</h3>
                <p style="font-size: 2rem; font-weight: bold; margin: 10px 0;">{'⭐⭐⭐' if quiz_percent >= 80 else '⭐⭐' if quiz_percent >= 60 else '⭐'}</p>
                <p style="font-size: 0.9rem; margin: 8px 0 0 0;">계속 화이팅! 📝</p>
            </div>
            """, 
            unsafe_allow_html=True
        )
        
    st.markdown("---")
    
    st.markdown(
        f"""
        <div style="background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%); border-radius: 15px; padding: 25px; border-left: 5px solid #ff6b6b; box-shadow: 0 4px 12px rgba(0,0,0,0.1);">
            <h3 style="color: #d63031; margin: 0 0 15px 0; font-weight: 700;">🌟 Sinu 튜터의 코멘트</h3>
            <p style="color: #333; font-size: 1rem; line-height: 1.8; margin: 0;">
                {'완벽한 정답률! 정말 훌륭합니다. 계속 이 정도의 실력을 유지하면 영어가 정말 쉬워질 거예요! 🎉' if quiz_percent == 100 else f'좋은 성과입니다! 정답률 {quiz_percent:.0f}%는 정말 대단해요. 틀린 부분을 다시 복습하면 더 완벽해질 거에요! 💡'}
            </p>
        </div>
        """,
        unsafe_allow_html=True
    )
    
    st.markdown("---")
    
    col1, col2 = st.columns(2)
    with col1:
        if st.button("🔄 다시 풀기", type="secondary", use_container_width=True):
            st.session_state.clear()
            time.sleep(1)
            st.rerun()
    
    with col2:
        if st.button("📧 교사에게 결과 전송하기", type="primary", use_container_width=True):
            st.balloons()
            st.success("✅ 전송 완료! 오늘 수업은 여기서 마무리합니다. 수고하셨습니다! 👋")
            
            # 세션 초기화 및 리로딩
            st.session_state.clear()
            time.sleep(1)
            st.rerun()

def render_chat_page():
    """메인 챗봇 인터페이스와 퀴즈를 랜더링합니다."""
    
    # 1. 챗봇 히스토리 랜더링 - 카톡 스타일
    st.markdown("""
    <style>
    /* 채팅창 커스텀 스타일 */
    .chat-message {
        display: flex;
        margin: 8px 0;
        gap: 8px;
    }
    
    .chat-message.user {
        flex-direction: row-reverse;
    }
    
    .chat-bubble {
        padding: 10px 14px;
        border-radius: 18px;
        max-width: 70%;
        word-wrap: break-word;
        font-size: 14px;
        line-height: 1.4;
    }
    
    .chat-bubble.model {
        background-color: #e8e8e8;
        color: #000;
    }
    
    .chat-bubble.user {
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        color: white;
    }
    
    .chat-avatar {
        font-size: 24px;
        display: flex;
        align-items: flex-end;
    }
    </style>
    """, unsafe_allow_html=True)
    
    chat_container = st.container(height=280, border=True)
    with chat_container:
        for i, message in enumerate(st.session_state.history):
            role_class = "user" if message["role"] == "user" else "model"
            avatar_char = "⭐" if message["role"] == "model" else "🧑‍🎓"
            
            st.markdown(f"""
            <div class="chat-message {role_class}">
                <div class="chat-avatar">{avatar_char}</div>
                <div class="chat-bubble {role_class}">
                    {message["text"]}
                </div>
            </div>
            """, unsafe_allow_html=True)
    
    # 2. 퀴즈 버튼 영역
    if not st.session_state.is_finished:
        st.markdown("---")
        st.markdown('<div class="scroll-target"></div>', unsafe_allow_html=True)
        st.markdown(f"**Sinu:** 이제 당신의 답변을 선택해주세요! 🎯 (문제 {st.session_state.current_q_index + 1}/{len(st.session_state.quiz_data)})")
        
        # st.session_state.options의 크기가 0보다 클 때만 columns를 호출합니다.
        if len(st.session_state.options) > 0:
            # 옵션이 3개 이상이면 2열, 2개 이하면 1열로 배치
            num_cols = min(2, len(st.session_state.options))
            cols = st.columns(num_cols)
            
            for idx, option in enumerate(st.session_state.options):
                # 키는 고유하게, 현재 퀴즈 인덱스를 기반으로 생성
                button_key = f"q_option_{st.session_state.current_q_index}_{idx}"
                col_idx = idx % num_cols
                
                if cols[col_idx].button(option, key=button_key, type="primary", use_container_width=True):
                    # 버튼 클릭 시 답변 처리 함수 호출
                    handle_answer(option)
        else:
            # 옵션이 없는 경우 대기 메시지 
            st.info("퀴즈를 로딩 중입니다...")


# 3. 종료 후 '결과 확인하기' 버튼
    if st.session_state.is_finished and not st.session_state.is_report_shown:
        st.markdown("---")
        st.markdown("**🎉 수업이 끝났어요!** 아래 버튼을 눌러서 학습 결과를 확인해 보세요! 👇")
        if st.button("📊 결과 확인하기", type="secondary", use_container_width=True):
            st.session_state.is_report_shown = True
            st.rerun()


# --- 메인 앱 실행 ---
def app_main():
    """Streamlit 애플리케이션의 메인 진입점"""
    
    # 앱 시작 시 첫 질문 자동 생성 및 history 업데이트
    if st.session_state.current_q_index == -1:
        # 첫 퀴즈 질문을 생성하고 인덱스 업데이트
        st.session_state.current_q_index = 0
        initial_question = generate_question()
        st.session_state.history.append({"role": "model", "text": initial_question})
    
    # 화면 랜더링 분기
    if st.session_state.is_finished and st.session_state.is_report_shown:
        render_final_report_page()
    else:
        render_chat_page()

if __name__ == "__main__":
    app_main()

