# Pipecat on Telnyx Edge

Deploy your [Pipecat](https://github.com/pipecat-ai/pipecat) voice agent to a
[Telnyx Edge](https://github.com/team-telnyx/edge-compute) function and give it
a phone number. There are no servers, containers or media infrastructure to
run: `telnyx-edge ship` uploads your code, and Telnyx builds it, hosts it and
connects your callers to it.

Everything a phone agent needs is on one platform, with one API key: the phone
number, the call audio, Deepgram Flux speech-to-text, LLM inference and
text-to-speech.

```python
pipeline = Pipeline([
    transport.input(),   # Telnyx call audio
    stt,                 # TelnyxSTTService (Flux)
    user_aggregator,
    llm,                 # OpenAILLMService -> GLM-5.3-Flash on Telnyx
    tts,                 # TelnyxTTSService
    transport.output(),
    assistant_aggregator,
])
```

It's the Pipecat you already know, with no framework changes and no new SDK.
The agent lives in `function/bot.py`.

## Deploy it

You need a [Telnyx account](https://telnyx.com/sign-up) with an API key and a
phone number, and the [`telnyx-edge` CLI](https://github.com/team-telnyx/edge-compute#install).

```bash
git clone https://github.com/team-telnyx/pipecat-edge
telnyx-edge new-func -l python -n my-pipecat-agent --from-dir pipecat-edge
cd my-pipecat-agent
telnyx-edge secrets add TELNYX_API_KEY <your-api-key>
telnyx-edge ship
```

`ship` prints your function's URL when the deploy finishes.

## Call it

In the Telnyx Portal, create a **TeXML Application** with the voice URL
`https://<your-function-url>/texml`, and assign your phone number to it. Then
call the number and start talking.

## Make it yours

Edit `function/bot.py` as you would any Pipecat bot. Prefer another provider?
Swap in its Pipecat service and add its key with `telnyx-edge secrets add`.
