graph TD
    %% スタイル定義
    classDef startEnd fill:#f9f,stroke:#333,stroke-width:2px;
    classDef process fill:#fff,stroke:#333,stroke-width:1px;
    classDef condition fill:#ffd,stroke:#333,stroke-width:1px;

    %% 各ステップ
    ST((1. START)):::startEnd --> S2[2. システム初期化<br>・INIT 0040H 呼び出し<br>・MODE 005BH 呼び出し]:::process
    S2 --> S3[3. レジスタ初期設定<br>・BC ← 除数<br>・HL ← 被除数<br>・DE ← 0000H 商カウンタ]:::process
    
    %% ループ構造
    S3 --> S4[4. ループ先頭 DIV_LP<br>・AND A によるキャリークリア]:::process
    S4 --> S5[5. 減算の実行<br>・SBC HL, BC 被除数－除数]:::process
    S5 --> S6{6. 判定分岐<br>キャリーフラグ C}:::condition
    
    %% 分岐
    S6 -- "C = 0 (引けた)" --> S7[7. 商の更新と反復<br>・INC DE 商+1<br>・JP DIV_LP]:::process
    S7 --> S4
    
    S6 -- "C = 1 (引けない)" --> S8[8. 補正 END_LP<br>・ADD HL, BC 足し戻し]:::process
    
    %% 後処理から終了
    S8 --> S9[9. 結果の格納<br>・商を 87C3H へ転送<br>・余りを 87C5H へ転送]:::process
    S9 --> S10[10. LED表示<br>・CALL DISP 0053H]:::process
    S10 --> ED((11. 終了<br>・RET 命令)):::startEnd
