# index
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>장욱진 미술관 네트워크 그래프</title>
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <style>
    body { 
      font-family: 'Malgun Gothic', sans-serif; 
      margin: 0;
      padding: 20px;
    }
    .node circle { 
      stroke: #fff; 
      stroke-width: 2px; 
      cursor: pointer;
    }
    .node text { 
      font-size: 10px; 
      pointer-events: none;
      text-anchor: middle;
      dominant-baseline: middle;
    }
    .link { 
      stroke: #999; 
      stroke-opacity: 0.6; 
      stroke-width: 1.5px;
    }
    .legend {
      position: absolute;
      top: 20px;
      right: 20px;
      background: white;
      border: 1px solid #ccc;
      padding: 10px;
      border-radius: 5px;
    }
    .legend-item {
      display: flex;
      align-items: center;
      margin-bottom: 5px;
    }
    .legend-color {
      width: 20px;
      height: 20px;
      margin-right: 10px;
      border-radius: 50%;
    }
  </style>
</head>
<body>
  <div class="legend">
    <div class="legend-item">
      <div class="legend-color" style="background-color: #ff7f7f;"></div>
      <span>교육기관</span>
    </div>
    <div class="legend-item">
      <div class="legend-color" style="background-color: #7fbf7f;"></div>
      <span>미술관</span>
    </div>
    <div class="legend-item">
      <div class="legend-color" style="background-color: #7f7fff;"></div>
      <span>전시</span>
    </div>
    <div class="legend-item">
      <div class="legend-color" style="background-color: #ffbf7f;"></div>
      <span>인물</span>
    </div>
    <div class="legend-item">
      <div class="legend-color" style="background-color: #bf7fff;"></div>
      <span>기타장소</span>
    </div>
  </div>
  
  <svg width="1200" height="800"></svg>
  
  <script>
    // 실제 데이터를 기반으로 한 노드 정의
    const nodes = [
      // 교육기관
      {id: "양정고등보통학교", group: "교육기관", type: "장소"},
      {id: "무사시노미술대학", group: "교육기관", type: "장소"},
      {id: "서울대학교", group: "교육기관", type: "장소"},
      
      // 미술관/전시기관
      {id: "국립현대미술관", group: "미술관", type: "장소"},
      {id: "장욱진고택", group: "미술관", type: "장소"},
      {id: "장욱진미술문화관", group: "미술관", type: "장소"},
      {id: "양주시립장욱진미술관", group: "미술관", type: "장소"},
      {id: "서울 인사아트센터", group: "미술관", type: "장소"},
      {id: "여수 예술의전당", group: "미술관", type: "장소"},
      {id: "서울 현대화랑", group: "미술관", type: "장소"},
      {id: "국립현대미술관 덕수궁", group: "미술관", type: "장소"},
      
      // 기타장소
      {id: "홍도", group: "기타장소", type: "장소"},
      
      // 전시
      {id: "장욱진의 삶과 예술세계", group: "전시", type: "전시"},
      {id: "장욱진 탄생, 인사동 라인에 서다", group: "전시", type: "전시"},
      {id: "한국 근현대미술 걸작전", group: "전시", type: "전시"},
      {id: "강건강의 아트토이展", group: "전시", type: "전시"},
      {id: "장욱진 30주기 기념전", group: "전시", type: "전시"},
      {id: "기억 전시관 고택", group: "전시", type: "전시"},
      {id: "장욱진의 봄날꽃숲", group: "전시", type: "전시"},
      
      // 인물
      {id: "장욱진", group: "인물", type: "인물"},
      {id: "유채원", group: "인물", type: "인물"},
      {id: "장효순", group: "인물", type: "인물"},
      {id: "오갑초", group: "인물", type: "인물"},
      {id: "윤나오", group: "인물", type: "인물"},
      {id: "김창실", group: "인물", type: "인물"},
      {id: "최복순", group: "인물", type: "인물"}
    ];

    // 실제 데이터를 기반으로 한 링크 정의
    const links = [
      // 장욱진과 장소 연결
      {source: "장욱진", target: "양정고등보통학교"},
      {source: "장욱진", target: "무사시노미술대학"},
      {source: "장욱진", target: "서울대학교"},
      {source: "장욱진", target: "홍도"},
      {source: "장욱진", target: "국립현대미술관"},
      {source: "장욱진", target: "장욱진고택"},
      {source: "장욱진", target: "장욱진미술문화관"},
      {source: "장욱진", target: "양주시립장욱진미술관"},
      
      // 유채원과 장소 연결
      {source: "유채원", target: "양정고등보통학교"},
      {source: "유채원", target: "무사시노미술대학"},
      {source: "유채원", target: "서울대학교"},
      {source: "유채원", target: "홍도"},
      {source: "유채원", target: "국립현대미술관"},
      {source: "유채원", target: "장욱진미술문화관"},
      {source: "유채원", target: "양주시립장욱진미술관"},
      
      // 기타 인물과 장소 연결
      {source: "장효순", target: "장욱진고택"},
      {source: "장효순", target: "장욱진미술문화관"},
      {source: "장효순", target: "양주시립장욱진미술관"},
      {source: "김창실", target: "장욱진고택"},
      {source: "최복순", target: "장욱진고택"},
      {source: "오갑초", target: "장욱진미술문화관"},
      {source: "윤나오", target: "양주시립장욱진미술관"},
      
      // 전시와 장소 연결
      {source: "장욱진의 삶과 예술세계", target: "양주시립장욱진미술관"},
      {source: "장욱진 탄생, 인사동 라인에 서다", target: "서울 인사아트센터"},
      {source: "한국 근현대미술 걸작전", target: "여수 예술의전당"},
      {source: "강건강의 아트토이展", target: "양주시립장욱진미술관"},
      {source: "장욱진 30주기 기념전", target: "서울 현대화랑"},
      {source: "기억 전시관 고택", target: "국립현대미술관 덕수궁"},
      {source: "장욱진의 봄날꽃숲", target: "양주시립장욱진미술관"},
      
      // 전시와 장욱진 연결
      {source: "장욱진", target: "장욱진의 삶과 예술세계"},
      {source: "장욱진", target: "장욱진 탄생, 인사동 라인에 서다"},
      {source: "장욱진", target: "한국 근현대미술 걸작전"},
      {source: "장욱진", target: "강건강의 아트토이展"},
      {source: "장욱진", target: "장욱진 30주기 기념전"},
      {source: "장욱진", target: "기억 전시관 고택"},
      {source: "장욱진", target: "장욱진의 봄날꽃숲"}
    ];

    const width = 1200, height = 800;

    const svg = d3.select("svg")
      .attr("width", width)
      .attr("height", height);

    // 그룹별 색상 정의
    const color = d3.scaleOrdinal()
      .domain(["교육기관", "미술관", "전시", "인물", "기타장소"])
      .range(["#ff7f7f", "#7fbf7f", "#7f7fff", "#ffbf7f", "#bf7fff"]);

    // 시뮬레이션 설정
    const simulation = d3.forceSimulation(nodes)
      .force("link", d3.forceLink(links).id(d => d.id).distance(100))
      .force("charge", d3.forceManyBody().strength(-300))
      .force("center", d3.forceCenter(width / 2, height / 2))
      .force("collision", d3.forceCollide().radius(30));

    // 링크 그리기
    const link = svg.append("g")
      .attr("class", "links")
      .selectAll("line")
      .data(links)
      .join("line")
      .attr("class", "link");

    // 노드 그룹 생성
    const node = svg.append("g")
      .attr("class", "nodes")
      .selectAll("g")
      .data(nodes)
      .join("g")
      .attr("class", "node")
      .call(drag(simulation));

    // 노드 원 그리기
    node.append("circle")
      .attr("r", d => d.type === "인물" ? 25 : d.type === "전시" ? 20 : 18)
      .attr("fill", d => color(d.group))
      .attr("stroke", "#fff")
      .attr("stroke-width", 2);

    // 노드 텍스트 추가
    node.append("text")
      .text(d => d.id.length > 8 ? d.id.substring(0, 8) + "..." : d.id)
      .attr("font-size", "10px")
      .attr("text-anchor", "middle")
      .attr("dy", ".35em")
      .attr("fill", "black");

    // 툴팁 기능
    node.append("title")
      .text(d => `${d.id} (${d.group})`);

    // 시뮬레이션 업데이트
    simulation.on("tick", () => {
      link
        .attr("x1", d => d.source.x)
        .attr("y1", d => d.source.y)
        .attr("x2", d => d.target.x)
        .attr("y2", d => d.target.y);

      node
        .attr("transform", d => `translate(${d.x},${d.y})`);
    });

    // 드래그 기능
    function drag(simulation) {
      function dragstarted(event, d) {
        if (!event.active) simulation.alphaTarget(0.3).restart();
        d.fx = d.x;
        d.fy = d.y;
      }

      function dragged(event, d) {
        d.fx = event.x;
        d.fy = event.y;
      }

      function dragended(event, d) {
        if (!event.active) simulation.alphaTarget(0);
        d.fx = null;
        d.fy = null;
      }

      return d3.drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended);
    }
  </script>
</body>
</html>
