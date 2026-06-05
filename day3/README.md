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

