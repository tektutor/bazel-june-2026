# Day 3

## Today's Agenda
<pre>
- [✅] Incremental builds and rebuild behavior
  - [✅] understanding dependency tracking and rebuild triggers
- [✅] C/C++ and embedded use cases
  - [✅] including cross-compilation (e.g., Linux ARM toolchain setup)
- [✅] CI/CD integration
  - [✅] configuring Bazel in pipelines such as Jenkins or GitHub Actions
- [✅] Docs-as-Code integration with Bazel
  - [✅] configuration and invocation approaches  
</pre>

## Lab - Bazel Monorepo example
```
cd ~/bazel-june-2026
git pull
cd day3/bazel-monorepo-example
tree
bazel query //...
cat app/cli/BUILD

bazel build //app/cli:cli
bazel run //app/cli:cli

/get health
login jegan root@123
get /whoami Bearer amVnYW4uYjZmY2ZlYmZkY2M4ZTgzNWNiZWU3YzczYTU0NWU1MDc0N2RiODhlOWQzNWZmYzhmMjM5OTI4MjhlZWE4OWEyYQ==
post /login username=jegan
post /login username=jegan&password=root@123
exit
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7105eba1-ad00-450c-8755-2d2c046509a4" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b8c9f8b2-eb2d-4ec8-b3c5-f68419897c15" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/9123ae60-e556-4e99-95d2-d817fed12c7b" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7cf588f1-f082-4488-9a11-d9e2f724b9e4" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f9444906-23a1-43f0-a243-52a50aa9516a" />


## Lab - Upgrade your gcc and g++ in Ubuntu
```
sudo apt update && apt install -y gcc-14 g++-14
gcc-14 --version
g++-14 --version
```

## Lab - Install docker
```
sudo apt install -y docker.io
sudo usermod -aG docker $USER
sudo su $USER
docker --version
docker images
```

## Lab - Bazel Tag
<pre>
- is a string label you can attach to a target to control how it behaves during
  testing, etc
- generally it is used to group automated test cases in Bazel 
</pre>
```
cd ~/bazel-june-2026
git pull
cd day3/bazel-tag
tree
cat MODULE.bazel
cat src/BUILD

bazel test //src:all --test_tag_filters=requires-network --test_output=all
bazel test //src:all --test_tag_filters=unit --test_output=all
bazel test //src:all --test_tag_filters=integration --test_output=all
bazel test //src:hello_slow_test --test_output=all
```

## Lab - Custom Test rules
```
cd ~/bazel-june-2026
git pull
cd day3/custom-test-rule

tree
cat MODULE.bazel
cat kvstore/BUILD.bazel
cat testinintegration_test.bzlg/BUILD.bazel
cat testing/

# Find how many test cases are there
bazel query 'tests(//...)'

# Run all test cases
bazel test //...

# Run only integration test cases
bazel test //... --test_tag_filters=integration

# Run only unit-test cases
bazel test //... --test_tag_filters=unit

# Run all test cases except integration test cases
bazel test //... --test_tag_filters=-integration
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/cc928b03-bbf6-49f1-9ff1-1fadfe1da8bf" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d5330283-f926-4c64-9ca9-565dc8978ab0" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5525788c-b2cc-4996-9d8b-f3db4647115f" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/719ea4d9-74a3-4a39-892f-52558eea2994" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7fcb95ec-42cc-48d3-9fdd-5e54412b5d46" />

## Lab - Code coverage
```
sudo apt update && sudo apt install -y lcov

cd ~/bazel-june-2026
git pull
cd day3/code-coverage
tree
cat MODULE.bazel
cat src/BUILD
cat .bazelversion

bazel clean
bazel test //src:hello_test
bazel coverage //src:hello_test \
  --instrumentation_filter=//src \
  --combined_report=lcov
lcov --summary bazel-out/_coverage/_coverage_report.dat 2>&1
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0d6ce9b8-e386-4096-ad77-42f47e77c3d7" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/9dcdc001-b58b-4459-85fb-729b7f975386" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/1802e8a7-54b7-45bd-9a43-86d4add4c165" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/18aabe35-feeb-46f2-a4b6-223e7d5e9a2e" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/9d4073c7-a532-4113-a574-5669cf2276c5" />


## Lab - Bazel Caching 
<pre>
How Bazel caching works ?
- Every action has an action key 
- a SHA-256 hash of all inputs
- Action Key = hash(
    source file contents  +
    compiler binary       +
    compiler flags        +
    environment variables +
    Bazel version
  )
- Bazel checks is the action key exists in cache
  - In case action key is found, Bazel downloads the output from cache and skips action
  - In case action key is not found, Bazel runs action, store the output in cache
</pre>

Check the local cache
```
bazel info output_base
bazel info repository_cache
```

Let's get our hands dirty
```
cd ~/bazel-june-2026/day3/bazel-caching
bazel clean --expunge

# Let's measure the first time build duration
time bazel build //src:hello

# Let's measure the second time build duration
time bazel build //src:hello

# Update the timestamp of a file
touch src/hello.cpp
time bazel build //src:hello

# Edit the hello.cpp
echo "// change" >> src/hello.cpp
time bazel build //src:hello

sed -i '$ d' src/hello.cpp
time bazel build //src:hello

# Inspect the local cache
du -sh $(bazel info output_base)

# See action cache directory
ls $(bazel info output_base)/action_cache/

# See how many entries are cached
find $(bazel info output_base)/action_cache -type f | wc -l

# See repository cache (downloaded tarballs)
du -sh $(bazel info repository_cache)
ls $(bazel info repository_cache)/content_addressable/sha256/ | head -10

# Verify cache key change
# Record action keys before
bazel aquery //src:hello --output=text 2>/dev/null \
  | grep "ActionKey" > /tmp/keys_before.txt

# Add a compiler flag
echo 'build --cxxopt=-DMY_FLAG' >> .bazelrc

# Record action keys after
bazel aquery //src:hello --output=text 2>/dev/null \
  | grep "ActionKey" > /tmp/keys_after.txt

# Compare — all keys changed because command string changed
diff /tmp/keys_before.txt /tmp/keys_after.txt

# Remove the flag
sed -i '/MY_FLAG/d' .bazelrc
```
#### Setup a remote cache server using docker
```
# Create the directory ( do it as a non-admin user )
mkdir -p /tmp/bazel-remote-cache

# Troubleshooting permission denied in case you have already created the folder as admin
sudo chown labuser:labuser /tmp/bazel-remote-cache
sudo chmod 777 /tmp/bazel-remote-cache

# Using Docker (easiest)
docker run -d \
  --name bazel-remote \
  -p 9090:8080 \
  -p 9092:9092 \
  -v /tmp/bazel-remote-cache:/data \
  buchgr/bazel-remote-cache \
  --dir=/data \
  --max_size=5

# Check if the docker container your created is running properly
docker ps
docker logs bazel-remote

# Find the IP address of the container
docker inspect bazel-remote | grep IPA
docker inspect -f {{.NetworkSettings.IPAddress}} bazel-remote

# Verify it is running
curl http://172.17.0.2:8080/status
curl http://localhost:9090
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/25b3b7d3-ccb4-4496-a089-cf6bf3bc880f" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/91a04138-ba4e-496f-b64b-0c7cda731b30" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7f97ffd8-8e39-4e8d-beb8-f2ebb643b8fb" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/230ddafe-3631-49f7-add7-6b8b2369613f" />


#### Configure remote cache server
```
cat >> .bazelrc << 'EOF'

# Remote cache configuration
build:remote-cache --remote_cache=http://172.17.0.2:8080
build:remote-cache --remote_upload_local_results=true
EOF
```

#### Perform clean build
```
cd ~/bazel-june-2026
git pull
cd day3/bazel-caching
bazel clean --expunge
time bazel build --config=remote-cache //src:hello

bazel clean --expunge
time bazel build --config=remote-cache //src:hello
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/89c5c1f1-0040-4901-97c6-206ca0fb1b49" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/abe2bd21-c052-4e09-8899-d14666120946" />

#### Verify cache hit/miss
```
bazel build --config=remote-cache //src:hello \
  --build_event_json_file=/tmp/build_events.json

# See cache statistics
cat /tmp/build_events.json | python3 -m json.tool \
  | grep -E "hit|remote|cache"
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ba1291e6-1067-40f6-8f5f-910e2ac66883" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/e4a5235a-eb31-4c20-81af-2b7bb1e2fff3" />

#### Force Bazel to always check remote cache first
```
# Always check remote cache, never use local cache
bazel build //src:hello \
  --remote_cache=http://172.17.0.2:8080 \
  --noremote_accept_cached=false \
  --remote_upload_local_results=true
```

#### Cache Optimization Techniques

.bazelrc
<pre>
# --- Remote cache: the local bazel-remote container ---
# gRPC endpoint you published with -p 9092:9092.
# build options are inherited by test, run, and coverage, so this
# one line covers `bazel test` too.
build --remote_cache=grpc://localhost:9092

# --- Test output ---
# Show output only when a test fails, instead of staying silent.
test --test_output=errors

# --- Tag-based test filtering (your demo) ---
#   bazel test //... --config=integration   runs only integration tests
#   bazel test //... --config=unit          runs only unit tests
test:integration --test_tag_filters=integration
test:unit --test_tag_filters=unit

# --- For later, when a second machine or CI joins ---
#   bazel build //... --config=dev   reads the cache, uploads nothing
#   bazel build //... --config=ci    reads and writes the cache
build:dev --noremote_upload_local_results
build:ci --remote_upload_local_results  
</pre>

Check if bazel is actually loading .bazelrc
```
bazel build //... --announce_rc
```

Confirm Cache server is reachable
```
curl http://localhost:9090/status
```

Cold build to populate the cache
```
bazel clean --expunge
bazel build //...
```

Confirm data actually landed in the container
```
du -sh /tmp/bazel-remote-cache
curl http://localhost:9090/status
```

The proof: expunge again, rebuild, watch remote hits.
```
bazel clean --expunge
bazel build //...
```

Contrast: turn the cache off and watch it get slow again
```
bazel clean --expunge
bazel build //... --remote_cache=
```

Demonstrate the tag-filter configs
```
bazel test //... --config=integration --announce_rc
```

## Lab - Bazel Documents as Code
```
cd ~/bazel-june-2026
git pull
cd day3/docs-as-code
cat docs/BUILD
bazel build //docs:training_notes
bazel build //docs:training_notes

bazel run //src:md2pdf -- --source https://github.com/tektutor/bazel-june-2026.git --folders day1 day2 day3 --output $PWD/notes.pdf
```

## Lab - Embedded application that supports different processor architectures(platforms)
```
# Install 64-bit ARM toolchain
sudo apt update
sudo apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu -y
sudo apt install -y qemu-user

cd ~/bazel-june-2026
git pull
cd day3/cross-compilation
cat MODULE.bazel
cat .bazelversion

bazel run //:hello --platforms=//:aarch64_linux --run_under="qemu-aarch64"

cd bazel-bin
file ./hello
qemu-aarch64 ./hello
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/836829a8-7e41-4e46-8cec-2a11f20164c1" />
