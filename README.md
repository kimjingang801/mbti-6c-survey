# mbti-6c-survey
Description (선택): “부족한 역량 키우기 Streamlit 설문 앱”
import streamlit as st
import json

st.set_page_config(page_title="부족한 역량 키우기", layout="wide")
st.title("부족한 역량 키우기 - 심리검사 설문지")

# --- 1. 이름 입력
name = st.text_input("이름을 입력하세요 (결과 저장용):")

# --- 2. MBTI 40문항
st.header("1. MBTI 성향 검사")
mbti_questions = [
    ("E","나는 새로운 사람들과 어울리는 것을 좋아한다."),
    ("I","나는 혼자 있는 시간이 필요하다."),
    ("J","나는 계획을 세우고 따르는 것을 선호한다."),
    ("P","나는 상황에 맞춰 유연하게 대처하는 편이다."),
    # ... (40문항 모두 같은 형식으로 추가)
]
mbti_scores = {k: 0 for k in ["E","I","S","N","T","F","J","P"]}
for i,(trait, q) in enumerate(mbti_questions, start=1):
    score = st.slider(f"{i}. {q}", 1, 5, 3, key=f"mbti{i}")
    mbti_scores[trait] += score

# --- 3. 6C 12문항
st.header("2. 6C 핵심 역량 진단")
sixc_categories = {
    "의사소통": [
        "나는 내 생각을 다른 사람에게 명확히 전달할 수 있다.",
        "나는 다양한 소통 방식(말, 글, 비언어 등)을 자유롭게 활용한다."
    ],
    # ... (나머지 5개 카테고리 모두 추가)
}
sixc_scores = {}
qnum = 41
for cat, qs in sixc_categories.items():
    total = 0
    for q in qs:
        total += st.slider(f"{qnum}. {q} ({cat})", 1, 5, 3, key=f"sixc{qnum}")
        qnum += 1
    sixc_scores[cat] = total

# --- 4. 취미 선택
st.header("3. 선호 취미 선택")
hobbies = ["독서","게임","요리","그림 그리기","운동","음악 감상","코딩","영상 편집","정리정돈","일기 쓰기"]
selected = st.multiselect("좋아하는 취미를 고르세요 (복수 선택 가능)", hobbies)

# --- 5. 일상 실천 5문항
st.header("4. 일상 실천 문항")
daily_questions = [
    "나는 하루에 한 번 내 생각을 글로 정리해본다.",
    # ... (총 5문항)
]
daily_total = 0
for idx, q in enumerate(daily_questions, start=53):
    daily_total += st.slider(f"{idx}. {q}", 1, 5, 3, key=f"daily{idx}")

# --- 6. 교육법 이해도 5문항
st.header("5. 교육법 이해도 문항")
edu_questions = [
    "나는 나의 약점을 파악하고 그것을 보완하려고 한다.",
    # ... (총 5문항)
]
edu_total = 0
for idx, q in enumerate(edu_questions, start=58):
    edu_total += st.slider(f"{idx}. {q}", 1, 5, 3, key=f"edu{idx}")

# --- 제출 버튼 & 결과
if st.button("설문 제출"):
    # MBTI 유형 결정
    mbti_type = ""
    mbti_type += "E" if mbti_scores["E"] >= mbti_scores["I"] else "I"
    mbti_type += "S" if mbti_scores["S"] >= mbti_scores["N"] else "N"
    mbti_type += "T" if mbti_scores["T"] >= mbti_scores["F"] else "F"
    mbti_type += "J" if mbti_scores["J"] >= mbti_scores["P"] else "P"

    st.subheader(f"✨ {name}님의 결과 ✨")
    st.write(f"- **MBTI 유형:** {mbti_type}")
    weakest = sorted(sixc_scores.items(), key=lambda x: x[1])[:2]
    st.write(f"- **부족한 역량 Top2:** {', '.join([w[0] for w in weakest])}")

    # 추천 로직 (예시)
    tips = {
        "자신감": ("발표훈련, 자기 피드백","일기 쓰기, 토론"),
        "비판적 사고": ("뉴스 분석 연습","퍼즐 게임, 토론"),
        # ... 나머지 카테고리
    }
    st.subheader("🔍 맞춤 추천")
    for cat, _ in weakest:
        st.write(f"**{cat}**")
        st.write(f"- 실천법: {tips[cat][0]}")
        st.write(f"- 추천 취미: {tips[cat][1]}")

    st.subheader("📊 점수 요약")
    st.write(f"- 일상 실천 총점: {daily_total}/25")
    st.write(f"- 교육 이해도 총점: {edu_total}/25")
    st.subheader("🎯 선택한 취미")
    st.write(", ".join(selected) if selected else "없음")
