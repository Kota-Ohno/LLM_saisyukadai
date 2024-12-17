これらのコードはgoogle colab L4環境を前提としています。

## 学習コード(SFT-llm-jp-3.ipynb)の使用方法
1. 学習データの合成
   hfdata_conbiner.ipynbを使用して、学習データ(combined_dataset.json)を 作成する。
2. 学習コードの実行
   huggingfaceでAPIキーの取得やモデルページの作成を行った後、HF_TOKENをコード内に入力してSFT-llm-jp-3.ipynbを実行する。 

## 推論コード(Model_Inference_v5.ipynb)の使用方法

1. 必要なライブラリのインストール
  - google colabで「Model_Inference_v5.ipynb」を開き、ランタイムをL4に設定します。
  - terminalを開き、以下のコードを順番に実行します。
    ```bash
    apt-get install git-lfs
    git lfs install
    ```
    ```bash
    git clone https://github.com/ggerganov/llama.cpp
    ```
    ```bash
    pip install -r ./llama.cpp/requirements.txt
    ```


2. モデルのダウンロード
    ```bash
    git clone https://huggingface.co/llm-jp/llm-jp-3-13b
    ```
    [llm-jp/llm-jp-tokenizer](https://github.com/llm-jp/llm-jp-tokenizer)から「llm-jp-tokenizer-100k.ver3.0b1.model」をダウンロードして、「llm-jp-3-13b」フォルダに「tokenizer.model」という名前で配置します

3. モデルの変換・量子化
  - 以下のコードを順番に実行します。
    ```bash
    python ./llama.cpp/convert_hf_to_gguf.py --outfile llm-jp-3-13b.gguf llm-jp-3-13b
    ```
    ```bash
    cd llama.cpp
    mkdir build
    cd build
    cmake ..
    cmake --build .
    cd ../
    cd ../
    ```
    ```bash
    ./llama.cpp/build/bin/llama-quantize ./llm-jp-3-13b.gguf ./llm-jp-3-13b-q8_0.gguf q8_0
    ```
    ```bash
    git clone https://huggingface.co/shakebenn/llm-jp-3-13b-SFT-LoRA
    python ./llama.cpp/convert_lora_to_gguf.py --base llm-jp-3-13b --outfile ./llm-jp-3-13b-SFT-LoRA.gguf llm-jp-3-13b-SFT-LoRA
    ```

4. 推論の実行
  - 「elyza-tasks-100-TV_0.jsonl」を推論コードと同階層にコピーする
  - 推論コード(Model_Inference_v5.ipynb)のすべてのセルを実行する
