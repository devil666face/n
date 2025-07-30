FROM intelanalytics/ipex-llm-inference-cpp-xpu:latest

ENV PATH="/llm:${PATH}"

RUN init-ollama

ENTRYPOINT ["ollama"]
CMD ["serve"]
