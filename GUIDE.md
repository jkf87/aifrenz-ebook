# Claude Code로 이슈페이퍼 제작부터 E-Book 배포까지

> 완전 가이드: 마크다운 → PDF → 3D 플립북 E-Book

---

## 개요

이 가이드는 Claude Code(바이브 코딩)를 활용하여 학술 이슈페이퍼를 제작하고, 웹 기반 E-Book으로 배포하는 전체 과정을 다룹니다.

### 완성된 결과물
- **PDF**: AIFRenz 이슈페이퍼 제1호 (30페이지)
- **E-Book**: https://jkf87.github.io/aifrenz-ebook/

### 사용 도구
- **Claude Code** - AI 바이브 코딩 도구
- **Python + ReportLab** - PDF 생성
- **DearFlip** - 3D 플립북 라이브러리
- **GitHub Pages** - 무료 호스팅

---

## PART 1: 이슈페이퍼 PDF 제작

### 1.1 원고 수집 및 마크다운 통합

4명의 기고자 원고(docx)를 하나의 마크다운 파일로 통합:

```
Claude Code 요청:
"이 4개의 docx 파일을 하나의 통합 마크다운 파일로 만들어줘.
YAML frontmatter에 발행정보를 넣고,
각 챕터별로 저자 정보를 포함해줘."
```

**결과물**: `이슈페이퍼_제1호_콘텐츠.md`

```yaml
---
title: "AIFRenz 이슈페이퍼"
volume: 1
date: "2026년 1월"
publisher: "AiFrenz 교육AI연구회"
authors:
  - name: "박권"
    affiliation: "호원중학교"
  - name: "정용석"
    affiliation: "서울신답초등학교"
  - name: "김혜진"
    affiliation: "증평초등학교"
  - name: "한준구"
    affiliation: "삼양초등학교"
---
```

### 1.2 디자인 철학 문서 생성

레퍼런스 이미지를 보여주며 디자인 시스템 요청:

```
Claude Code 요청:
"이 이미지들을 참고해서 AIFrenz 이슈페이퍼의 디자인 철학 문서를 만들어줘.
브랜드 컬러, 타이포그래피, 레이아웃 시스템을 정의해줘."
```

**결과물**: `aifrenz_design_system_v4.md`

주요 디자인 요소:
- **컨셉**: "봄날 새벽 (Fresh Spring Dawn)"
- **그라디언트 배경**: 연한 블루 → 크림 → 연한 핑크
- **액센트 컬러**: 봄꽃 팔레트 (Blossom Pink, Mint Fresh, Lavender)
- **리본 패턴**: 부드러운 베지어 곡선

### 1.3 PDF 변환기 개발

```
Claude Code 요청:
"마크다운을 PDF로 변환하는 스크립트를 만들어줘.
표지 디자인 시스템과 동일한 컬러를 사용하고,
각 챕터는 새 페이지에서 시작하게 해줘."
```

**결과물**: `md_to_pdf_v4.py`

주요 기능:
- YAML frontmatter 파싱
- 헤더/테이블/이미지/코드블록 렌더링
- 챕터별 페이지 나눔
- 표지/목차/뒷표지 자동 생성

### 1.4 실시간 수정 (이터레이션)

PDF 생성 후 피드백을 통해 수정:

```
"5페이지 그림 1-1 사이즈를 줄여서 4페이지로 변경해줘."
→ 이미지 크기 문법 추가: ![alt](img.png){max_height=50mm}

"김혜진 선생님 부분 들여쓰기가 안 맞아."
→ 텍스트 너비 계산으로 정확한 들여쓰기 적용

"CC BY 4.0 아이콘을 표지에 추가해줘."
→ 아이콘 다운로드 및 표지 하단에 배치
```

### 1.5 PDF 생성 명령어

```bash
cd issuepaper
python3 slides/md_to_pdf_v4.py "이슈페이퍼_제1호_콘텐츠.md" "dist/AIFRenz_이슈페이퍼_제1호_v4.pdf"
```

---

## PART 2: E-Book 변환 및 배포

### 2.1 플립북 라이브러리 선택

PDF를 3D 플립북으로 변환하는 오픈소스 조사:

| 라이브러리 | 특징 | 라이선스 |
|-----------|------|----------|
| **DearFlip** | 3D 효과, PDF 직접 로드 | CC BY-NC-ND 4.0 |
| pdf-to-flipbook-converter | Python 기반, 오프라인 지원 | MIT |
| flipbook-viewer | 18KB 경량 | MIT |

**선택**: DearFlip (가장 화려한 3D 효과)

### 2.2 프로젝트 설정

```bash
# DearFlip 클론
git clone https://github.com/dearhive/dearflip-js-flipbook.git ebook-viewer
cd ebook-viewer

# 불필요한 파일 정리
rm -rf .git README.md LICENSE package.json
rm -rf examples

# PDF 폴더 생성 및 복사
mkdir books
cp /path/to/AIFRenz_이슈페이퍼_제1호_v4.pdf books/issuepaper.pdf
```

### 2.3 HTML 페이지 작성

**index.html**:
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <title>AIFRenz 이슈페이퍼 제1호</title>
  <meta name="viewport" content="width=device-width, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">

  <!-- Open Graph 태그 -->
  <meta property="og:title" content="AIFRenz 이슈페이퍼 제1호">
  <meta property="og:description" content="AI 교육 연구 커뮤니티">
  <meta property="og:image" content="https://jkf87.github.io/aifrenz-ebook/books/cover.png">

  <!-- DearFlip CSS -->
  <link href="dflip/css/dflip.min.css" rel="stylesheet">
  <link href="dflip/css/themify-icons.min.css" rel="stylesheet">

  <style>
    body {
      margin: 0;
      background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
      min-height: 100vh;
    }
    .header {
      text-align: center;
      padding: 15px;
      background: rgba(0,0,0,0.3);
      color: white;
    }
    .book-container {
      width: 100%;
      height: calc(100vh - 60px);
    }
  </style>
</head>
<body>

<div class="header">
  <h1>AIFRenz 이슈페이퍼 제1호</h1>
</div>

<div class="book-container">
  <div class="_df_book"
       height="100%"
       webgl="true"
       backgroundcolor="#1a1a2e"
       source="books/issuepaper.pdf">
  </div>
</div>

<!-- DearFlip JS -->
<script src="dflip/js/libs/jquery.min.js"></script>
<script src="dflip/js/dflip.min.js"></script>

</body>
</html>
```

### 2.4 표지 썸네일 추출 (OG 태그용)

```python
import fitz  # PyMuPDF
doc = fitz.open('books/issuepaper.pdf')
page = doc[0]
pix = page.get_pixmap(matrix=fitz.Matrix(2, 2))
pix.save('books/cover.png')
```

### 2.5 GitHub Pages 배포

```bash
# Git 초기화 및 커밋
git init
git add -A
git commit -m "AIFRenz 이슈페이퍼 E-Book Viewer"

# GitHub 레포 생성 및 푸시
gh repo create aifrenz-ebook --public --source=. --push

# GitHub Pages 활성화
gh api repos/USERNAME/aifrenz-ebook/pages -X POST --input - <<< '{"source":{"branch":"main","path":"/"}}'
```

### 2.6 최종 폴더 구조

```
ebook-viewer/
├── index.html          # 메인 페이지
├── books/
│   ├── issuepaper.pdf  # 이슈페이퍼 PDF
│   └── cover.png       # OG 태그용 썸네일
├── dflip/              # DearFlip 라이브러리
│   ├── css/
│   ├── js/
│   ├── fonts/
│   ├── images/
│   └── sound/
├── .nojekyll           # GitHub Pages 설정
└── .gitignore
```

---

## PART 3: 전체 워크플로우 요약

```
┌─────────────────────────────────────────────────────────────┐
│                    이슈페이퍼 제작 파이프라인                    │
└─────────────────────────────────────────────────────────────┘

1. 원고 수집 (docx)
       ↓
2. 마크다운 통합 (이슈페이퍼_제1호_콘텐츠.md)
       ↓
3. 디자인 시스템 정의 (aifrenz_design_system_v4.md)
       ↓
4. PDF 변환기 개발 (md_to_pdf_v4.py)
       ↓
5. PDF 생성 및 수정 반복
       ↓
6. E-Book 변환 (DearFlip)
       ↓
7. GitHub Pages 배포
       ↓
8. 완성! 🎉
```

---

## 핵심 명령어 정리

### Claude Code 설치
```bash
npm install -g @anthropic-ai/claude-code
```

### PDF 생성
```bash
python3 slides/md_to_pdf_v4.py "콘텐츠.md" "output.pdf"
```

### E-Book 로컬 테스트
```bash
cd ebook-viewer
python3 -m http.server 8080
# 브라우저에서 http://localhost:8080 접속
```

### GitHub Pages 배포
```bash
git add -A && git commit -m "업데이트" && git push
```

---

## 다음 호 발행 시

1. **마크다운 파일만 새로 작성** (`이슈페이퍼_제2호_콘텐츠.md`)
2. **PDF 생성**: `python3 md_to_pdf_v4.py "제2호.md" "제2호.pdf"`
3. **E-Book 업데이트**: PDF 교체 후 `git push`

디자인 시스템이 코드에 녹아있어 **5분 만에 새 호 발행 가능**!

---

## 참고 자료

- [DearFlip GitHub](https://github.com/dearhive/dearflip-js-flipbook)
- [Claude Code 공식 문서](https://docs.anthropic.com/claude-code)
- [ReportLab 문서](https://www.reportlab.com/docs/reportlab-userguide.pdf)

---

*이 가이드는 Claude Code로 작성되었습니다.*
*Designed with Claude Code*
