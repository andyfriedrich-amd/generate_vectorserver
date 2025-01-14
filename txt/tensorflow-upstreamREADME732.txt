<h1>MLIR HLO mlir_bisect</h1>
<p>This is a test case reduction tool, similar in purpose to <code>mlir-reduce</code>, but
specific to the <code>mlir-interpreter</code> infrastructure. In particular, reductions can
depend on concrete values encountered during execution, and reductions can (and
usually do) generate multiple candidates.</p>
<p>For example, the <code>ReplaceOpWithConstant</code> reduction will attempt to replace each
op with each of its results. If the op is in a loop, each execution will be a
candidate for replacement.</p>
<h2>Using this tool</h2>
<ol>
<li>
<p>Run a JAX test with snapshots enabled:</p>
<p><code>bazel test some-jax-test
  --test_env=XLA_FLAGS="--xla_cpu_use_xla_runtime --xla_dump_to=/tmp/dump
  --xla_dump_hlo_snapshots" --test_filter=SomeSpecific.Test
  --test_sharding_strategy=disabled --test_strategy=local</code></p>
</li>
<li>
<p>Figure out the culprit module and pass (sorry, no automation yet):</p>
<p><code>bazel run tensorflow/compiler/xla/mlir/tools/mlir_replay:mlir_replay -- \
  --mlir-compilation-trace=/tmp/dump/module_0000.jit__something.mlir-trace.pb \
  --hlo-snapshot=/tmp/dump/module_0000.jit__something.snapshot.0.pb \
  --print-changes-only \
  --execution-trace-dir=/tmp/execution</code></p>
<p>You should see a pass after which results change. You'll want to use the
.mlir file in <code>/tmp/execution</code> corresponding to the pass <em>before</em> that with
the bisect tool.</p>
<p>Note: If the failing pass is bufferization, you may have to use an earlier
snapshot, e.g. before EmptyTensorToAllocTensor.
1.  Run bisect:</p>
<p><code>bazel run tensorflow/compiler/xla/mlir/tools/mlir_bisect:mlir-bisect -- \
  --hlo-snapshot=/tmp/dump/module_0000.jit_something.snapshot.0.pb \
  --pass-pipeline="builtin.module(empty-tensor-to-alloc-tensor,one-shot-bufferize{allow-return-allocs bufferize-function-boundaries create-deallocs=0})" \
  /tmp/execution/0052.ScalarizationPass.mlir</code></p>
</li>
</ol>
<h2>Adding a reduction</h2>
<p>To add a reduction, create a function that generates the candidates and register
it:</p>
<p>```
SmallVector<OwningOpRef\<ModuleOp>>
FrobulateAndDefenestrate(BisectState&amp;, dialect::SomeOp some_op) {
  auto [cloned_module_1, cloned_op_1] = CloneModuleFor(some_op);
  Frobulate(cloned_op_1);</p>
<p>auto [cloned_module_2, cloned_op_2] = CloneModuleFor(some_op);
  Defenestrate(cloned_op_2);</p>
<p>return {cloned_module_1, cloned_module_2};
}</p>
<p>REGISTER_MLIR_REDUCE_STRATEGY(FrobulateAndDefenestrate);
```</p>
<p>Then, add a test for the strategy. Make sure your strategy is linked into
mlir-bisect and has <code>alwayslink</code> set.</p>
<p>```
// RUN: mlir-bisect %s --debug-strategy=FrobulateAndDefenestrate | FileCheck %s</p>
<p>func.func @main() {
  dialect.some_op()
}</p>
<p>// CHECK: func @main()
// CHECK-NEXT: frobulated</p>
<p>// CHECK: func @main()
// CHECK-NEXT: defenestrated
```</p>
<p><code>--debug-strategy</code> will print all candidates generated by the given strategy.</p>