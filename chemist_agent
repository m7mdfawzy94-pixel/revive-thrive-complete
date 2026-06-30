import os
import streamlit as st
import anthropic

# ─── Page config ──────────────────────────────────────────────────────────────
st.set_page_config(
    page_title="Chemist Agent — وكيل الصيدلي",
    page_icon="💊",
    layout="wide",
    initial_sidebar_state="expanded",
)

# ─── Product catalog ──────────────────────────────────────────────────────────
CATALOG: dict = {
    "X-FIT": {
        "icon": "🔥",
        "color": "#D94A4A",
        "label": "حرق الدهون وتثبيط الشهية",
        "products": [
            {"name": "Java Burn",      "desc": "محسّن أيض بتركيبة القهوة الخضراء",      "dosage": "كبسولة واحدة يومياً مع الإفطار",    "pack": "30 كبسولة"},
            {"name": "Xeners",         "desc": "كبسولات حرق متقدمة مزدوجة المفعول",      "dosage": "كبسولة صباحاً وأخرى مساءً",          "pack": "60 كبسولة"},
            {"name": "Herbal Max",     "desc": "تركيبة عشبية طبيعية للتنحيف",            "dosage": "كبسولة قبل الأكل بـ30 دقيقة",        "pack": "30 كبسولة"},
            {"name": "Green Coffee",   "desc": "قهوة خضراء للحرق والطاقة",               "dosage": "كبسولة مرتين يومياً مع الماء",        "pack": "60 كبسولة"},
            {"name": "ZORIL Black",    "desc": "تركيبة سوداء متخصصة للحرق المكثف",      "dosage": "كبسولة قبل التمرين بـ20 دقيقة",      "pack": "30 كبسولة"},
            {"name": "Crown",          "desc": "تاج الكبسولات الغذائية المتكاملة",        "dosage": "كبسولتان يومياً",                     "pack": "60 كبسولة"},
            {"name": "American Diet",  "desc": "نظام التنحيف الأمريكي المتكامل",          "dosage": "كبسولة مع الإفطار",                   "pack": "30 كبسولة"},
            {"name": "Ayurslim",       "desc": "تركيبة أيورفيدية هندية للتنحيف",          "dosage": "كبسولتان مرتين يومياً",               "pack": "60 كبسولة"},
            {"name": "X.FIT",          "desc": "النظام المتكامل لرشاقة الجسم",            "dosage": "كبسولة صباحاً على الريق",             "pack": "30 كبسولة"},
            {"name": "MiDiVAST",       "desc": "خاصية تثبيط الشهية المتقدمة",             "dosage": "كبسولة قبل الأكل",                    "pack": "30 كبسولة"},
            {"name": "Liora Horizon",  "desc": "أفق جديد لرشاقة الجسم المثالية",          "dosage": "كبسولة يومياً",                       "pack": "30 كبسولة"},
        ],
    },
    "Healthy Diet": {
        "icon": "🥗",
        "color": "#2ECC71",
        "label": "الحمية الصحية والتنحيف المتوازن",
        "products": [
            {"name": "Easy Slim",    "desc": "تنحيف سهل وآمن بدون تعقيدات",          "dosage": "كبسولة مرتين يومياً",          "pack": "60 كبسولة"},
            {"name": "Orga Slim",    "desc": "تركيبة عضوية للتنحيف التدريجي",        "dosage": "كبسولة يومياً مع الماء",        "pack": "30 كبسولة"},
            {"name": "Oxford",       "desc": "تركيبة علمية متوازنة للرجيم",          "dosage": "كبسولتان قبل الغداء",           "pack": "60 كبسولة"},
            {"name": "Ripped Freak", "desc": "التركيبة المتطورة لنحت الجسم",         "dosage": "كبسولة قبل التمرين",            "pack": "60 كبسولة"},
        ],
    },
    "Premium Diet": {
        "icon": "⭐",
        "color": "#C6A84E",
        "label": "منتجات التخسيس البريميوم",
        "products": [
            {"name": "Slimo Advanced",   "desc": "تنحيف متقدم بتقنية حديثة",              "dosage": "كبسولة مرتين يومياً",   "pack": "60 كبسولة"},
            {"name": "UltraSlim Pro",    "desc": "البروتوكول الاحترافي للتنحيف",           "dosage": "كبسولتان يومياً",        "pack": "60 كبسولة"},
            {"name": "Fatless Forever",  "desc": "الحل الدائم لمشكلة الدهون",              "dosage": "كبسولة مع كل وجبة",     "pack": "90 كبسولة"},
            {"name": "Moringa Fort",     "desc": "قوة المورينجا الطبيعية للصحة",           "dosage": "كبسولة صباحاً على الريق","pack": "30 كبسولة"},
            {"name": "Regetrim",         "desc": "إعادة ضبط الجسم والتوازن الهرموني",      "dosage": "كبسولتان مساءً",         "pack": "60 كبسولة"},
            {"name": "Recover on Keto",  "desc": "دعم نظام الكيتو والتعافي السريع",        "dosage": "3 كبسولات يومياً",       "pack": "90 كبسولة"},
        ],
    },
    "Ampoules": {
        "icon": "💉",
        "color": "#4A90D9",
        "label": "أمبولات وحقن العلاج والتجميل",
        "products": [
            {"name": "Konjac",           "desc": "أمبول كونجاك لشد الجسم وتكسير الدهون",  "dosage": "حقنة أسبوعياً عضلياً",       "pack": "أمبول 5 مل"},
            {"name": "Meta Max Plus",    "desc": "دعم الأيض المكثف عبر الحقن",             "dosage": "حقنة كل أسبوعين عضلياً",     "pack": "أمبول 2 مل"},
            {"name": "Kilvatt Solution", "desc": "محلول كيلفات لشد وتقوية الجسم",          "dosage": "حقنة أسبوعياً عضلياً",       "pack": "أمبول 10 مل"},
        ],
    },
}

SYSTEM_PROMPT = """أنت وكيل صيدلي متخصص ومحترف تعمل مع شركة Revive Thrive لتوزيع المكملات الغذائية والمستحضرات الصيدلانية بالجملة في مصر. عملاؤك هم العيادات والتجار.

مهامك الأساسية:
1. الإجابة على أسئلة العملاء حول المنتجات والمكملات الغذائية
2. تحليل التفاعلات المحتملة بين المنتجات المختلفة
3. تقديم توجيهات الجرعات المناسبة بحسب حالة العميل
4. تقديم توصيات مناسبة للحالات المختلفة

قواعد صارمة:
- تحدث دائماً بالعربية (الفصحى أو العامية المصرية بحسب سياق العميل)
- انصح دائماً بمراجعة الطبيب المختص في الحالات الحرجة
- قدم معلومات دقيقة وموثوقة علمياً بدون مبالغة
- نبّه على التحذيرات والموانع بوضوح

كتالوج Revive Thrive المتاح:
• X-FIT (حرق الدهون): Java Burn, Xeners, Herbal Max, Green Coffee, ZORIL Black, Crown, American Diet, Ayurslim, X.FIT, MiDiVAST, Liora Horizon
• Healthy Diet (حمية صحية): Easy Slim, Orga Slim, Oxford, Ripped Freak
• Premium Diet (بريميوم): Slimo Advanced, UltraSlim Pro, Fatless Forever, Moringa Fort, Regetrim, Recover on Keto
• Ampoules (أمبولات): Konjac, Meta Max Plus, Kilvatt Solution"""


# ─── Helpers ──────────────────────────────────────────────────────────────────

def get_client() -> anthropic.Anthropic:
    key = os.environ.get("ANTHROPIC_API_KEY", "")
    if not key:
        try:
            key = st.secrets["ANTHROPIC_API_KEY"]
        except Exception:
            pass
    if not key:
        st.error("⚠️ لم يتم العثور على ANTHROPIC_API_KEY. أضفه في المتغيرات البيئية أو .streamlit/secrets.toml")
        st.stop()
    return anthropic.Anthropic(api_key=key)


def stream_ai(messages: list[dict]) -> str:
    client = get_client()
    with client.messages.stream(
        model="claude-sonnet-4-6",
        max_tokens=1500,
        system=SYSTEM_PROMPT,
        messages=messages,
    ) as stream:
        result = ""
        for text in stream.text_stream:
            result += text
            yield text
    return result


# ─── CSS ──────────────────────────────────────────────────────────────────────

def inject_css() -> None:
    st.markdown("""
<style>
@import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap');

html, body, [class*="css"], .stApp {
    font-family: 'Cairo', sans-serif !important;
    direction: rtl;
    background: #0a0a0a;
    color: #f5f0e8;
}

.block-container { padding-top: 1rem; }

/* Sidebar */
[data-testid="stSidebar"] {
    background: #111111;
    border-left: 1px solid #2a2a2a;
}

/* Radio buttons */
[data-testid="stSidebar"] label { color: #f5f0e8 !important; }

/* Inputs */
.stTextInput input, .stTextArea textarea, .stSelectbox select {
    background: #1a1a1a !important;
    color: #f5f0e8 !important;
    border-color: #2a2a2a !important;
}

/* Buttons */
.stButton > button[kind="primary"] {
    background: #C6A84E;
    color: #0a0a0a;
    border: none;
    font-weight: 700;
    border-radius: 50px;
    padding: .5rem 1.4rem;
}
.stButton > button[kind="primary"]:hover { background: #E8D48B; }
.stButton > button:not([kind="primary"]) {
    background: transparent;
    color: #a09882;
    border: 1px solid #2a2a2a;
    border-radius: 50px;
}

/* Product card */
.product-card {
    background: #111111;
    border: 1px solid #2a2a2a;
    border-radius: 14px;
    padding: 14px;
    margin-bottom: 12px;
    transition: transform .2s;
}
.product-card:hover { transform: translateY(-3px); }

/* Chat */
[data-testid="stChatMessage"] { background: #111111; border-radius: 12px; }

/* Divider */
hr { border-color: #2a2a2a; }
</style>
""", unsafe_allow_html=True)


# ─── Pages ────────────────────────────────────────────────────────────────────

def page_chat() -> None:
    st.markdown("## 💬 وكيل الصيدلي الذكي")
    st.caption("اسأل عن أي منتج، تفاعل، جرعة، أو توصية طبية متعلقة بكتالوج Revive Thrive.")

    if "chat_history" not in st.session_state:
        st.session_state.chat_history = []

    for msg in st.session_state.chat_history:
        avatar = "💊" if msg["role"] == "assistant" else "👤"
        with st.chat_message(msg["role"], avatar=avatar):
            st.markdown(msg["content"])

    if prompt := st.chat_input("اسأل الصيدلي…  مثال: ما الفرق بين Java Burn و Green Coffee؟"):
        st.session_state.chat_history.append({"role": "user", "content": prompt})
        with st.chat_message("user", avatar="👤"):
            st.markdown(prompt)

        api_msgs = [{"role": m["role"], "content": m["content"]}
                    for m in st.session_state.chat_history]

        with st.chat_message("assistant", avatar="💊"):
            response = st.write_stream(stream_ai(api_msgs))

        st.session_state.chat_history.append({"role": "assistant", "content": response})

    if st.session_state.chat_history and st.button("🗑️ مسح المحادثة"):
        st.session_state.chat_history = []
        st.rerun()


def page_catalog() -> None:
    st.markdown("## 📦 كتالوج المنتجات")

    search = st.text_input("🔍 بحث عن منتج…", placeholder="اكتب اسم المنتج")

    for category, data in CATALOG.items():
        # Filter products if searching
        products = data["products"]
        if search:
            products = [p for p in products
                        if search.lower() in p["name"].lower() or search in p["desc"]]
        if not products:
            continue

        header = f"{data['icon']} **{category}** — {data['label']}"
        with st.expander(header, expanded=bool(search)):
            cols = st.columns(3)
            for i, p in enumerate(products):
                with cols[i % 3]:
                    st.markdown(f"""
<div class="product-card" style="border-top:3px solid {data['color']};">
  <h4 style="color:{data['color']};margin:0 0 6px 0;">{p['name']}</h4>
  <p style="color:#a09882;font-size:.85rem;margin:0 0 8px 0;">{p['desc']}</p>
  <p style="color:#6b6355;font-size:.78rem;margin:0;"><b>الجرعة:</b> {p['dosage']}</p>
  <p style="color:#6b6355;font-size:.78rem;margin:2px 0 0;"><b>التعبئة:</b> {p['pack']}</p>
</div>
""", unsafe_allow_html=True)


def page_interactions() -> None:
    st.markdown("## ⚗️ محاكاة التفاعلات")
    st.info("اختر منتجين من الكتالوج لفحص التفاعلات المحتملة بينهما.")

    all_names = [
        f"{p['name']} ({cat})"
        for cat, data in CATALOG.items()
        for p in data["products"]
    ]

    c1, c2 = st.columns(2)
    with c1:
        a = st.selectbox("المنتج الأول", all_names, index=0)
    with c2:
        b = st.selectbox("المنتج الثاني", all_names, index=1)

    extra = st.text_input(
        "أدوية أو مكملات إضافية (اختياري)",
        placeholder="مثال: أسبرين 100 مج، فيتامين د، ليفوثيروكسين…"
    )

    if st.button("🔬 فحص التفاعلات", type="primary"):
        query = f"افحص التفاعلات المحتملة بين {a} و {b}"
        if extra:
            query += f" مع {extra}"
        query += ". قدّم التحليل بشكل منظم: التفاعلات المعروفة، درجة الخطورة، والتوصية."

        with st.chat_message("assistant", avatar="⚗️"):
            st.write_stream(stream_ai([{"role": "user", "content": query}]))


def page_dosage() -> None:
    st.markdown("## ⚖️ حاسبة الجرعات")
    st.caption("احسب الجرعة المناسبة بناءً على بيانات المريض أو العميل.")

    c1, c2, c3 = st.columns(3)
    with c1:
        weight = st.number_input("الوزن (كجم)", min_value=30, max_value=250, value=75)
    with c2:
        age = st.number_input("العمر (سنة)", min_value=15, max_value=90, value=35)
    with c3:
        gender = st.selectbox("الجنس", ["ذكر", "أنثى"])

    all_names = [
        f"{p['name']} ({cat})"
        for cat, data in CATALOG.items()
        for p in data["products"]
    ]
    product = st.selectbox("اختر المنتج", all_names)

    conditions = st.multiselect(
        "حالات صحية موجودة",
        [
            "ضغط دم مرتفع", "سكري النوع الثاني", "أمراض القلب والشرايين",
            "حمل أو رضاعة", "حساسية من الكافيين", "قصور كلوي",
            "قصور كبدي", "مشاكل في الغدة الدرقية", "أنيميا",
        ],
    )

    goal = st.radio(
        "هدف الاستخدام",
        ["تخفيف الوزن", "رفع الطاقة", "نحت الجسم", "دعم الأيض"],
        horizontal=True,
    )

    if st.button("احسب الجرعة المناسبة", type="primary"):
        cond_str = "، ".join(conditions) if conditions else "لا توجد حالات مرضية مذكورة"
        query = f"""احسب الجرعة المناسبة من {product} للحالة التالية:
- الوزن: {weight} كجم | العمر: {age} سنة | الجنس: {gender}
- الحالات الصحية: {cond_str}
- الهدف: {goal}

الرجاء تقديم: الجرعة اليومية الموصى بها، وقت التناول، مدة الكورس، التحذيرات الخاصة بالحالة، وما إذا كان المنتج مناسباً أصلاً."""

        with st.chat_message("assistant", avatar="⚖️"):
            st.write_stream(stream_ai([{"role": "user", "content": query}]))


def page_protocols() -> None:
    st.markdown("## 📋 بروتوكولات علاجية جاهزة")
    st.caption("بروتوكولات تجمع أكثر من منتج لأهداف محددة — مناسبة لتوصيات العيادات.")

    protocols = {
        "برنامج حرق الدهون المكثف (4 أسابيع)": {
            "products": ["Java Burn", "ZORIL Black", "Konjac"],
            "desc": "برنامج متكامل لحرق الدهون يجمع كبسولات الأيض مع الحقن الداعمة.",
        },
        "برنامج الكيتو المدعوم": {
            "products": ["Recover on Keto", "Moringa Fort", "Easy Slim"],
            "desc": "دعم كامل لنظام الكيتو الغذائي مع التعافي وتعويض المغذيات.",
        },
        "برنامج نحت الجسم الاحترافي": {
            "products": ["Ripped Freak", "UltraSlim Pro", "Meta Max Plus"],
            "desc": "للرياضيين والمهتمين بنحت الجسم مع الحفاظ على الكتلة العضلية.",
        },
        "برنامج التنحيف التدريجي الآمن": {
            "products": ["Orga Slim", "Moringa Fort", "Fatless Forever"],
            "desc": "مناسب للحالات التي تحتاج تنحيفاً تدريجياً وآمناً بدون ضغط على الجسم.",
        },
    }

    selected_protocol = st.selectbox("اختر البروتوكول", list(protocols.keys()))
    proto = protocols[selected_protocol]

    st.markdown(f"**المنتجات:** {' ← '.join(proto['products'])}")
    st.markdown(f"**الوصف:** {proto['desc']}")

    if st.button("🤖 اشرح البروتوكول بالتفصيل", type="primary"):
        query = f"""اشرح البروتوكول التالي بالتفصيل لصيدلاني يريد توصيته للعملاء:
البروتوكول: {selected_protocol}
المنتجات: {', '.join(proto['products'])}
الوصف: {proto['desc']}

قدّم: جدول الاستخدام اليومي، ترتيب الجرعات، التحذيرات، ومن هو المستفيد الأمثل."""

        with st.chat_message("assistant", avatar="📋"):
            st.write_stream(stream_ai([{"role": "user", "content": query}]))


# ─── Main ─────────────────────────────────────────────────────────────────────

def main() -> None:
    inject_css()

    # Header
    st.markdown("""
<div style="text-align:center;padding:1.8rem 0 .5rem;">
  <h1 style="font-family:'Cairo',sans-serif;font-size:2.4rem;font-weight:900;
      background:linear-gradient(135deg,#f5f0e8,#e8d48b 50%,#f5f0e8);
      -webkit-background-clip:text;-webkit-text-fill-color:transparent;margin:0;">
    💊 Chemist Agent
  </h1>
  <p style="color:#a09882;margin:.4rem 0 0;font-size:.95rem;">
    وكيل الصيدلي الذكي — مدعوم بالذكاء الاصطناعي | Revive Thrive
  </p>
</div>
<hr style="border-color:#2a2a2a;margin:.5rem 0 1.5rem;">
""", unsafe_allow_html=True)

    # Sidebar
    with st.sidebar:
        st.markdown("""
<div style="text-align:center;padding:.5rem 0 1rem;">
  <span style="font-size:2rem;">💊</span>
  <h3 style="color:#C6A84E;margin:.3rem 0 0;font-family:'Cairo',sans-serif;">Chemist Agent</h3>
  <p style="color:#6b6355;font-size:.78rem;margin:0;">Revive Thrive — Egypt</p>
</div>
""", unsafe_allow_html=True)

        st.divider()
        page = st.radio(
            "القائمة",
            [
                "💬 وكيل الصيدلي",
                "📦 كتالوج المنتجات",
                "⚗️ محاكاة التفاعلات",
                "⚖️ حاسبة الجرعات",
                "📋 بروتوكولات جاهزة",
            ],
            label_visibility="collapsed",
        )

        st.divider()
        st.markdown("""
<div style="text-align:center;">
  <p style="color:#6b6355;font-size:.78rem;margin:0 0 .6rem;">تواصل مع Revive Thrive</p>
  <a href="https://wa.me/201500343014?text=محتاج%20كتالوج%20أسعار%20الجملة"
     target="_blank"
     style="display:inline-block;background:#25D366;color:white;padding:.5rem 1.1rem;
            border-radius:50px;text-decoration:none;font-weight:700;font-size:.85rem;">
    📱 واتساب
  </a>
</div>
""", unsafe_allow_html=True)

        st.divider()
        st.markdown(
            "<p style='color:#6b6355;font-size:.72rem;text-align:center;'>"
            "المعلومات المقدمة للأغراض المهنية فقط.<br>دائماً راجع الطبيب المختص.</p>",
            unsafe_allow_html=True,
        )

    # Route
    if page == "💬 وكيل الصيدلي":
        page_chat()
    elif page == "📦 كتالوج المنتجات":
        page_catalog()
    elif page == "⚗️ محاكاة التفاعلات":
        page_interactions()
    elif page == "⚖️ حاسبة الجرعات":
        page_dosage()
    elif page == "📋 بروتوكولات جاهزة":
        page_protocols()


if __name__ == "__main__":
    main()
