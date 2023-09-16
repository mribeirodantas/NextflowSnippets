# <img width=25 height=25 src="https://github.com/nextflow-io/trademark/blob/master/nextflow-icon-128x128.png?raw=true"> Nextflow Snippets

Snippet is a programming term for a small region of re-usable source code, machine code, or text, and Nextflow is both a Domain-Specific Language for writing data pipelines and a pipeline orchestrator. For more information about Nextflow, check the official [site](https://www.nextflow.io) and [documentation](https://www.nextflow.io/docs/latest/). For community training material, [this link](https://training.nextflow.io) is a greaaaaaat place to start! :smiley:

This repository contains a collection of numerous solutions I have worked on throughout the past months as a Nextflow and nf-core Developer Advocate at [Seqera Labs](https://seqera.io) providing community support to numerous users around the world. I haven't saved all the snippets I created, but as I have now answered over 500 questions in this period, there are indeed quite a few I have kept when I found the solution interesting enough :satisfied:

## List of snippets by category
Below, you'll find links to Markdown documents in this repository explaining the issue followed by the snippet that solves it.

### Using Channel Operators
  - [Get first N elements from a channel](snippets/get_first_N_from_channel.md)
  - [Get last N elements from a channel](snippets/get_last_N_from_channel.md)
  - [Filter items within a list in tuples within your channels](snippets/filter_items_within_tuples_within_your_channel.md)
  - [Filter tuples within your channel](snippets/filter_tuples_within_your_channel.md)
  - [Flatten multiple-item tuple to 2-item tuple keeping key](snippets/flatten_multiple_item_tuples_to_simple_tuple.md)
  - [Recreate `countBy` operator for channels consisting of single elements or tuples](snippets/countBy.md)
  - [Set operations with channels](snippets/set_operations.md)
  - [Filter output channel elements based on file size or file content](snippets/filter_based_on_size_content.md)
  - [Sort lexicographically paths in a list within a tuple](snippets/sort_lexicographically_list_in_tuple.md)
  - [Provide multiple paths from a channel as a string to a command](snippets/joining_paths_as_string_to_command.md)
  - [Guarantee tuple structure in a channel](snippets/guarantee_tuple_structure.md)
### Misc
  - [Prepare assets without running the pipeline](snippets/prepare_assets_wo_running_pipeline.md)
  - [Organize output files according to some pattern, and organize everything else in a different path](snippets/organize_publishdir_rest.md)
  - [Capture multiple values provided with a pipeline parameter](snippets/capture_multi_values_pipeline_parameter.md)
  - [Query attributes of objects](snippets/query_object_attributes.md)
  - [Piping with multiple processes at the same time](snippets/pipe_multiple_processes.md)
  - [Get the `publishDir` paths for a specific task](snippets/get_task_publishDir.md)
  - [Request the maximum number of available CPU cores for a process](snippets/get_max_avail_cpus.md)
  - [Pass config settings as a command line argument instead of a config file](snippets/config_cmd_instead_file.md)
  - [Multiple instances of the same file provided to the same task](snippets/same_file_multiple_times.md)
  - [Save configuration files used in a specific run](snippets/save_configs.md)
  - [Compose configurations in the `nextflow.config` file](snippets/composing_nextflow_config.md)
  - [Run some processes outside containers](snippets/no_container_per_process.md)
  ## Nextflow Patterns
  If you're looking for more snippets, you can find plenty at [Nextflow Patterns](https://nextflow-io.github.io/patterns/).
