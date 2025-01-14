<h1>PJRT - Uniform Device API</h1>
<p>PJRT C API is the uniform Device API that we want to add to the ML ecosystem.
The long term vision is that: (1) frameworks (TF, JAX, etc.) will call PJRT,
which has device-specific implementations that are opaque to the frameworks; (2)
each device focuses on implementing PJRT APIs as PJRT plugins, which can be
opaque to the frameworks.</p>
<h2>Communication channel</h2>
<ul>
<li>Please file issues in the <a href="https://github.com/openxla/xla">OpenXla/xla repo</a>.</li>
<li>Join discussion in the #pjrt-plugin channel of the <a href="https://github.com/openxla/iree/#communication-channels">IREE discord server</a>.</li>
</ul>
<h2>Resources</h2>
<ul>
<li><a href="https://github.com/openxla/openxla-pjrt-plugin">OpenXLA/IREE PJRT plugin implementation</a></li>
<li><a href="https://docs.google.com/document/d/1KV_p6aa-u5v_U71_SdtyBDdH-qncY-dUr47-wIUJQ_s/edit?resourcekey=0-mzaosg6Id0JLpEoT_O47LA#heading=h.xsz0btxjkdo3">PJRT integration guide</a></li>
<li><a href="https://docs.google.com/document/d/1Qdptisz1tUPGn1qFAVgCV2omnfjN01zoQPwKLdlizas/edit">PJRT Plugin Mechanism design doc</a></li>
</ul>