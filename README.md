# OBTAIN: Real-Time Beat Tracking in Audio Signals
This repo contains the code for the application developed in the paper: 
[OBTAIN: Real-Time Beat Tracking in Audio Signals](http://www.ijsps.com/uploadfile/2017/1220/20171220034151817.pdf)
by Ali Mottaghi, Kayhan Behdin, Ashkan Esmaeili, Mohammadreza Heydari, and Farokh
Marvasti.


We design a system in order to perform the real-time beat tracking for an audio
signal. We use Onset Strength Signal (OSS) to detect the onsets and estimate the
tempos. Then, we form Cumulative Beat Strength Signal (CBSS) by taking advantage
of OSS and estimated tempos. Next, we perform peak detection by extracting the
periodic sequence of beats among all CBSS peaks. In simulations, we can see that
our proposed algorithm, Online Beat TrAckINg (OBTAIN), outperforms state-of-art
results in terms of prediction accuracy while maintaining comparable and
practical computational complexity. The real-time performance is tractable
visually as illustrated in the simulations. 

## Notes

The beat tracking algorithm C code was auto-generated from a MATLAB sketch with
some modifications by the OBTAIN team. That code lives in
`/Beat_Tracking_Stem_ert_rtw`. The beat tracking has a hardcoded frame size of
1024 samples. It may also expect samplerate 44100, bit depth 16 and 1 channel
(at least that's what the GUI is hardcoded to but it's possible the algorithm
itself doesn't depend on those values).

The beat tracking code expects several global functions to exist with `Extern C`,
which it will call during processing:

- `BT_Beat_SetPower(double timeOfRecord, float BeatPower)`
  <br/>Called with the time of the next beat?
  <br/>See `/AlgorithmInterface/AlgorithmInterface_Audio.cpp#340`
- `BT_Audio_SourceSamplesToDSP(double* outSamples, int SampleCount)`
  <br/>Called to populate the audio sample to analyze
  <br/>See `/AlgorithmInterface/AlgorithmInterface_Audio.cpp#119`
- `BT_Audio_SinkSamplesToPlay(const double* outSamples, int SampleCount)`
  <br/>Called with the output audio from the DSP audio. I'm not sure why it outputs
  audio but it might just be an audio through for when the GUI plays back audio
  from a file.
  <br/>See `/AlgorithmInterface/AlgorithmInterface_Audio.cpp#220`
- `BT_Audio_AfterDSPStep()`
  <br/>Called after DSP processing for a single frame is complete.
  <br/>See `/AlgorithmInterface/AlgorithmInterface_Audio.cpp#395`
- `BT_GlobalGraph_Add(int Idx, int Lane, double val)`
  <br/>Called with logging information during the DSP processing
    | Idx | Lane | Description         |
    | --- | ---- | ------------------- |
    | 0   | 0    | beats detected      |
    | 0   | 1    | CBSS score          |
    | 1   | 0    | flux function value |

  See `/AppGUI/Graphs.cpp#50`
- `BT_GlobalGraph_SetTime(double T)`
  <br/>Called with the duration each frame took to process?
  <br/>See `/AppGUI/Graphs.cpp#63`

To run the beat tracking algorithm, you can follow the steps in
`BeatTrackingMain` in file `/Beat_TrackingStem_ert_rtw/ert_main.cpp`. The code
in that file was modified to handle the specifics of the OBTAIN GUI. However,
the main steps would be the same:

- On startup, Call `Beat_Tracking_Stem_initialize()`
- In a loop, call `Beat_Tracking_Stem_step()`
- On destroy, call `Beat_Tracking_Stem_terminate()`

