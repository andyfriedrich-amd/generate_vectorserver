<h3>Cached requirements and consolidation of conda and pip installation</h3>
<p>At the moment, the installation of conda and pip dependencies happens at
different places in the CI depending at the whim of different
developers, which makes it very challenging to handle issues like
network flakiness or upstream dependency failures gracefully. So, this
center directory is created to gradually include all the conda environment
and pip requirement files that are used to setup CI jobs. Not only it
gives a clear picture of all the dependencies required by different CI
jobs, but it also allows them to be cached properly to improve CI
reliability.</p>
<p>The list of support files are as follows:</p>
<ul>
<li>Conda:</li>
<li>conda-env-iOS. This is used by iOS build and test jobs to setup the
    conda environment</li>
<li>conda-env-macOS-ARM64. This is used by MacOS (m1, arm64) build and
    test jobs to setup the conda environment</li>
<li>conda-env-macOS-X64. This is use by MacOS (x86-64) build and test
    jobs to setup the conda environment</li>
<li>conda-env-Linux-X64. This is used by Linux buck build and test jobs
    to setup the conda environment</li>
<li>Pip:</li>
<li>pip-requirements-iOS.txt. This is used by iOS build and test jobs to
    setup the pip environment</li>
<li>pip-requirements-macOS.txt. This is used by MacOS build and test jobs to
    setup the pip environment</li>
</ul>