```text
conda list
    // list packages

conda env list
    // list environments

conda info -e
    // list environments

conda create -n your_env_name python=X.X
    // create a new environment

conda install -n your_env_name [package]
    // install packages into your_env_name

deactivate          // Windows
source deactivate   // Linux

conda remove -n your_env_name --all
    // delete your_env_name

conda remove --name your_env_name package_name
    // delete some package from your_env_name

conda create -n new_env --clone old_env
	// clone old_env as new_env
	
conda install --offline the_path_of_the_package_tar_gz
	// install some package offline
	

```