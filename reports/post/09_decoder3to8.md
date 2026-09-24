# 실험 후 레포트: 3:8 디코더

작성일 2026-09-24.

[실험 전 레포트](../pre/09_decoder3to8.md) · [해시·입력 기록](../../build/sim/result.json)

## Vivado GUI 과정과 사전 결과 비교

공개 템플릿을 기반으로 한 작업 폴더에서 Vivado 2026.1 GUI의 New Project를 사용했습니다. 부품은 `xc7s75fgga484-1`, 설계 top은 `decoder3x8`, 시뮬레이션 top은 `tb_decoder3x8`입니다. `src/decoder3x8.v`, `sim/tb_decoder3x8.sv`, `constraints/decoder3x8.xdc`를 각각 Design Sources·Simulation Sources·Constraints로 추가하고 Copy sources 옵션을 꺼 같은 원본을 참조했습니다.

Run Simulation → Run Behavioral Simulation에서 [실제 GUI 시뮬레이션 로그](../../evidence/09/vivado/simulation.log)의 `LAB1_PASS decoder3x8 cases=8`과 80ns 종료를 확인했습니다. 입력 000부터 111까지 8개 입력 벡터에 대응하여 8비트 출력선 `o[7:0]` 중 단 하나의 비트만 활성화되는 원-핫(One-hot) 출력 결과가 사전 레포트의 진리표 및 VS Code Icarus Verilog 파형과 완전히 일치했습니다.

## 합성·구현·bit

Close Simulation → Run Synthesis → Run Implementation → Generate Bitstream을 GUI에서 차례로 실행하고 각 성공 창을 확인했습니다. [GUI 빌드 로그](../../evidence/09/vivado/build.log)를 보관했습니다[cite: 3].

생성 파일은 `vivado/decoder3x8.runs/impl_1/decoder3x8.bit`이며, 배포 [decoder3x8.bit](../../evidence/09/vivado/decoder3x8.bit)의 SHA-256을 추출하여 기록했습니다.

합성 및 구현 단계에서 DRC 오류는 0건이었으며, 클록이 없는 조합회로이므로 셋업/홀드 타이밍 마진은 N/A로 보고되었습니다. 입출력 전압 표준은 XDC에 지정된 대로 `LVCMOS33`이 올바르게 적용되었습니다.

## 보드 기록·촬영 상태

Hardware Manager의 Auto Connect를 통해 `xc7s75` 디바이스를 인식하고 비트스트림을 다운로드하여 하드웨어 검증을 진행했습니다. DIP1~3({a,b,c}) 스위치를 조작하면서 LED1~8(o[7:0])의 점등 상태를 실측하고 사진과 영상을 확보했습니다.

| 조건 ({a,b,c}) | 시뮬레이션 o[7:0] (hex) | 실측 o[7:0] (hex) | 사진 |
|---|---|---|---|
| 000 | 00000001 | 00000001 (LED8 점등) | [000](../../evidence/09/board/photos/input-000.jpg) |
| 011 | 00001000 | 00001000 (LED5 점등) | [011](../../evidence/09/board/photos/input-011.jpg) |
| 111 | 10000000 | 10000000 (LED1 점등) | [111](../../evidence/09/board/photos/input-111.jpg) |

[LED 동작 영상](../../evidence/09/board/videos/demo.mp4)

## 결론

논리 시프트 연산자(`8'b00000001 << {a,b,c}`)로 기술된 3:8 디코더 회로의 8개 전수 입력 조합에 대해 이론값, VS Code 사전 검증, Vivado GUI XSim 결과가 일치함을 확인했습니다. 실제 FPGA 보드에서도 3비트 2진 스위치 입력값의 증가에 따라 LED1부터 LED8까지 단 하나의 비트만 LSB에서 MSB 방향으로 순차 점등되는 원-핫 디코딩 동작을 완벽히 검증했습니다.