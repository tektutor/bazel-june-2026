# Day 3

## Today's Agenda
<pre>
- [ ] Incremental builds and rebuild behavior
  - [ ] understanding dependency tracking and rebuild triggers
- [ ] C/C++ and embedded use cases
  - [ ] including cross-compilation (e.g., Linux ARM toolchain setup)
- [ ] CI/CD integration
  - [ ] configuring Bazel in pipelines such as Jenkins or GitHub Actions
- [ ] Docs-as-Code integration with Bazel
  - [ ] configuration and invocation approaches  
</pre>

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
cd daye/code-coverage
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
bazel clean --expunge
time bazel build --config=remote-cache //src:hello

bazel clean --expunge
time bazel build --config=remote-cache //src:hello
```

#### Verify cache hit/miss
```
bazel build --config=remote-cache //src:hello \
  --build_event_json_file=/tmp/build_events.json

# See cache statistics
cat /tmp/build_events.json | python3 -m json.tool \
  | grep -E "cacheHit|cacheMiss|remoteCache"
```

#### Force Bazel to always check remote cache first
```
# Always check remote cache, never use local cache
bazel build //src:hello \
  --remote_cache=http://172.17.0.2:8080 \
  --noremote_accept_cached=false \
  --remote_upload_local_results=true
```
