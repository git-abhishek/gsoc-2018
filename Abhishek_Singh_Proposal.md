# Improve Test Coverage and Test Performance

## Abstract

This project focuses to improve yt's test framework. At present, yt's code coverage is only 26% and the test runtime is approximately 45 minutes. The aim of this project is to increase code coverage and reduce test runtime.

First, I propose the use of [Coveralls](https://coveralls.io/), which is a tool to monitor the code coverage and is free for open source repositories. This would not only help in analyzing the key areas that need immediate attention for coverage but will also help in maintaining higher code coverage for future developments.

yt's test suite could be divided into three areas, namely, python unit tests, cython test cases and answer testing. I will enhance the yt test suite by writing test cases for the flows that are not being tested currently. Runtime of tests could be improved by optimizing (or reducing) answer testing and image comparisons tests for visualization and volume rendering modules. This project also focuses on streamlining test cases for different geometries and data styles to improve runtime of tests. Furthermore, runtime of test suites vary on linux and OSX and giving us a scope of improvement.

## Technical Details

### 1. Coveralls Integration

Coveralls is a tool to analyze code coverage with each pull request. The main advantage of this tool is that with each pull request one can know how much of the new code is being been tested.

I created a sample [GitHub project](https://github.com/git-abhishek/Poc-Coverage) to show Coveralls integration with Travis CI. Initially, I created two functions ``add(a,b)`` and ``sub(a,b)`` with a unit test case for only ``add`` function. Due to this, the code coverage came out to be 86%. Then I created a PR to add the test case for ``sub(a,b)`` function. The effect of this PR could be easily analyzed at the [coveralls build page](https://coveralls.io/builds/16036140).

![Fig 1. Coveralls build page for the PR](https://github.com/git-abhishek/Poc-Coverage/blob/master/image/covealls%20build%20of%20PR.PNG "Fig 1. Coveralls build page for the PR")

In addition, we also get a small summary at the [PR page](https://github.com/git-abhishek/Poc-Coverage/pull/2) depicting the overall code coverage owing to these changes. Using this we can easily track if the new changes are being properly tested or not, whether code coverage is improving which each commit or not.

![Fig 2. Coveralls summary at the PR page](https://github.com/git-abhishek/Poc-Coverage/blob/master/image/Coverage%20Summary%20PR.PNG "Fig 2. Coveralls summary at the PR page")

### 2. Current Code Coverage Analysis and Improvement

yt is written in Python as well as Cython. Though there is already a testing framework for both, there are few key issues with them, described as follows:
  * Code coverage in both launguages is less than X%
  * Previous attempts have been made for Cython code coverage but that has resulted in appreciably slowing down the build runtime (done by [Kacper Kowalik](https://github.com/Xarthisius))
  * Answer testing takes a lot of time to run

Present code coverage ([with](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage_branching/index.html) and [without](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/index.html) branching) of yt is 26%. The commands used for this purpose are as follows:

```
nosetests --with-coverage --cover-inclusive --cover-erase --cover-html --cover-package=yt
nosetests --with-coverage --cover-branches --cover-inclusive --cover-erase --cover-html --cover-package=yt
```

Starting point to expand test cases is by adding support for different geometries (cartesian, cylindrical, and spherical coordinates) and data styles (particle data, mesh data, including uniform resolution, octree, patch AMR, and unstructured meshes). Using the existing test functions `fake_random_ds`, `fake_amr_ds`, and `fake_particle_ds` I can define a `fake_test_datasets` generators. Using this a given functionality could be tested for different underlying geometries and data sytles.

Using this analysis, I plan to identify yt modules that require more unit testing. This breakdown has been listed in [Phase 1](https://github.com/git-abhishek/gsoc-2018/blob/master/Abhishek_Singh_Proposal.md#phase-1) and [Phase 2](https://github.com/git-abhishek/gsoc-2018/blob/master/Abhishek_Singh_Proposal.md#phase-2) weekly schedule.

I aim to use [Coverage](https://coverage.readthedocs.io/en/coverage-4.5.1/) and [Nose Timer](https://pypi.python.org/pypi/nose-timer) tools with the existing Nose framework. Coverage tells us which areas of code are untouched by a given code flow and thus helps in improving code coverage. Using nose-timer, we can get the runtime of a test case and thus it would help me in publishing before and after reports for test runtime.

### 3. Improving Test Runtime

* Unit tests could be made faster by identifying the tests that are doing heavy lifting and then simplfying them. For example, tests could be using big dataset where the same could be done by using a smaller dataset. Similarly, test cases could be using a dataset when not required.

* Besides the gain achieved by pruning the unit tests, **test runtime** could be reduced heavly by improving the answer testing and image comparison tests. Instead of these heavy tests, expected values of the function could be compared with a pre-computed value. For example:
  * Instead of pixel by pixel comparison of images, one could compare the [perceptual hash](http://phash.org/) values.
  * Using Matplotlib's [image testing](https://matplotlib.org/1.5.3/devel/testing.html) approach we can compare the gold images (known-correct images) with the generated image [ImageComparisonTest](https://github.com/matplotlib/matplotlib/blob/5e52ce11ab59176a467dd2c68db8c5e1fbc20a24/lib/matplotlib/testing/decorators.py#L282) and thus remove the current plot/image answer tests like [FieldValuesTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L410), [AllFieldValuesTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L443), [ProjectionValuesTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L467), [PixelizedProjectionValuesTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L518), [GridValuesTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L555), [VerifySimulationSameTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L577), [GridHierarchyTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L597), [ParentageRelationshipsTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L614), [SimulatedHaloMassFunctionTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L638), [AnalyticHaloMassFunctionTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L664), [VRImageComparisonTest](https://github.com/yt-project/yt/blob/51cf1ce0825dd1e9ef6bbbf8e9a83a82a768cd7e/yt/utilities/answer_testing/framework.py#L710) and others.

* As pointed by [Colin Marc](https://github.com/colinmarc), OSX runtime is more than the linux environment. One of the reasons for this is due to the `before_install` section of the travis.yml file. In OSX it takes 277.30s however, in linux it takes 7.13s (for a random run). Thus a small amount of time could be spent in this task, if its possible to reduce this and get a straight away gain of 4 minutes in each build.


## Schedule of Deliverables

### **Community Bonding Period**
  
  * April 23 - May 14
    * Connect with mentors and other community members
    * Discuss more on yt's testing framework
    * Research more on effective testing frameworks
    * Understand test code flow for different geometries and data styles
    * Understand in detail [yt's testing framework](http://yt-project.org/doc/developing/testing.html#testing)
    * Set up a blog post and write the first blog

### **Common Task Each Week**
  
  * Write blog post showing progress, code flow understanding (1 per week)
  * Maintain a public Wiki listing tasks done each day (everyday)
  * Write narrative documents in yt repository (if needed)
  * Work on yt's bug/issue list (optional, if time permits)
  
### **Phase 1**
  
  **Deliverables**
  
    1. Coveralls Integration
    2. Finish up writing test cases for currently untouched code, increasing code coverage from 26% to maximum possible
    3. Improve the Stream frontend by making it compatible with different geometries and data types
    4. Enhancements in data_objects, geometry, frontends, units and fields modules
  
  * Week 1: [May 14 - May 18]
    * Integrate Coveralls to yt
    * Work on data_objects module
    * [image_array.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_image_array_py.html), 
[region_expression.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_region_expression_py.html), 
[particle_filters.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_particle_filters_py.html), 
[analyzer_objects.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_analyzer_objects_py.html), 
[grid_patch.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_grid_patch_py.html), 
[unstructured_mesh.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_unstructured_mesh_py.html), 
[particle_io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_particle_io_py.html), 
[selection_data_containers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_selection_data_containers_py.html), 
[derived_quantities.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_derived_quantities_py.html), 
[profiles.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_profiles_py.html), 
[particle_trajectories.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_particle_trajectories_py.html), 
[octree_subset.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_octree_subset_py.html), 
[time_series.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_time_series_py.html), 
[static_output.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_static_output_py.html), 
[data_containers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_data_containers_py.html), 
[construction_data_containers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_data_objects_construction_data_containers_py.html)

  * Week 2: [May 21 - May 25]
    * Work on geometry module
    * [oct_geometry_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_oct_geometry_handler_py.html), 
[unstructured_mesh_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_unstructured_mesh_handler_py.html), 
[particle_geometry_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_particle_geometry_handler_py.html), 
[spec_cube_coordinates.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_spec_cube_coordinates_py.html), 
[cartesian_coordinates.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_cartesian_coordinates_py.html), 
[spherical_coordinates.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_spherical_coordinates_py.html), 
[cylindrical_coordinates.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_cylindrical_coordinates_py.html), 
[coordinate_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_coordinate_handler_py.html), 
[grid_geometry_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_grid_geometry_handler_py.html), 
[object_finding_mixin.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_object_finding_mixin_py.html), 
[geographic_coordinates.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_coordinates_geographic_coordinates_py.html), 
[geometry_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_geometry_geometry_handler_py.html)
    * Cython Code: fake_octree.pyx, grid_container.pyx, grid_visitors.pyx, oct_container.pyx, oct_visitors.pyx, particle_deposit.pyx, particle_oct_container.pyx, particle_smooth.pyx, selection_routines.pyx
        
        
  * Week 3: [May 28 - June 1]
    * Work on frontends module
    * [fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_sph_fields_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_sph_data_structures_py.html), 
[definitions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_rockstar_definitions_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_data_structures_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ytdata_fields_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_eagle_fields_py.html), 
[simulation_handling.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_simulation_handling_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_sdf_fields_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_tipsy_fields_py.html), 
[utilities.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ytdata_utilities_py.html), 
[misc.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_open_pmd_misc_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_fits_fields_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_eagle_data_structures_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gamer_fields_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_artio_io_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_fields_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_halo_catalog_data_structures_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_pp_fields_py.html), 
[util.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_exodus_ii_util_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gizmo_fields_py.html), 
[config.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_config_py.html), 
[misc.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_p_misc_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_moab_io_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_art_fields_py.html), 
[simulation_handling.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_exodus_ii_simulation_handling_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_exodus_ii_io_py.html),  
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_fields_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_flash_fields_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_stream_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gdf_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_rockstar_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_http_stream_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_pp_io_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_artio_fields_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_fields_py.html), 
[hilbert.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_hilbert_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ahf_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_fits_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_halo_catalog_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_http_stream_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_rockstar_io_py.html), 
[owls_ion_tables.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_owls_ion_tables_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ahf_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gamer_io_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_fields_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_p_io_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_open_pmd_fields_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_open_pmd_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_moab_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_sdf_data_structures_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_fields_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_chombo_fields_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_subfind_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gdf_data_structures_py.html), 
[particle_handlers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_particle_handlers_py.html), 
[fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_boxlib_fields_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_owls_subfind_io_py.html), 
[misc.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_fits_misc_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_tipsy_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gamer_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_sdf_io_py.html), 
[field_handlers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_field_handlers_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_exodus_ii_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_flash_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_boxlib_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_chombo_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_p_data_structures_py.html), 
[simulation_handling.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_simulation_handling_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ytdata_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_stream_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_pp_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_fof_io_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_tipsy_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_data_structures_py.html), 
[simulation_handling.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_simulation_handling_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_artio_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_gadget_fof_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_flash_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_fits_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ramses_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_open_pmd_data_structures_py.html), 
[io.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_art_io_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_athena_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ytdata_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_chombo_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_enzo_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_art_data_structures_py.html), 
[data_structures.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_boxlib_data_structures_py.html)
    * Cython Code: artio/_artio_caller.pyx
    
  * Week 4: [June 4 - June 8]
    * Work on units module
    * [pint_conversions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_pint_conversions_py.html), 
[unit_registry.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_unit_registry_py.html), 
[dimensions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_dimensions_py.html), 
[unit_systems.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_unit_systems_py.html), 
[equivalencies.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_equivalencies_py.html), 
[unit_object.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_unit_object_py.html), 
[yt_array.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_units_yt_array_py.html)
    
  * Week 5: [June 11 - June 15]
    * Work on fields module
    * [cosmology_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_cosmology_fields_py.html), 
[astro_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_astro_fields_py.html), 
[field_plugin_registry.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_field_plugin_registry_py.html), 
[interpolated_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_interpolated_fields_py.html), 
[geometric_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_geometric_fields_py.html), 
[vector_operations.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_vector_operations_py.html), 
[fluid_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_fluid_fields_py.html), 
[local_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_local_fields_py.html), 
[magnetic_field.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_magnetic_field_py.html), 
[field_exceptions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_field_exceptions_py.html), 
[field_detector.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_field_detector_py.html), 
[field_info_container.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_field_info_container_py.html), 
[particle_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_particle_fields_py.html), 
[species_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_species_fields_py.html), 
[derived_field.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_derived_field_py.html), 
[xray_emission_fields.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_fields_xray_emission_fields_py.html)
    

### **Phase 2**
  
  **Deliverables**
  
    1. Improve volume rendering and visualization modules answer testing and image comparison by an expected answer
       of a test case
    2. Improvement in utilites python and cython modules with respect to higher code coverage and reduction in test runtime
  
  * Week 6: [June 18 - June 22]
    * Work on visualization module
    * [particle_plots.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_particle_plots_py.html), 
[line_plot.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_line_plot_py.html), 
[tick_locators.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_tick_locators_py.html), 
[base_plot_types.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_base_plot_types_py.html), 
[streamlines.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_streamlines_py.html), 
[image_writer.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_image_writer_py.html), 
[fixed_resolution.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_fixed_resolution_py.html), 
[color_maps.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_color_maps_py.html), 
[fits_image.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_fits_image_py.html), 
[plot_container.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_plot_container_py.html), 
[plot_window.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_plot_window_py.html), 
[profile_plotter.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_profile_plotter_py.html), 
[plot_modifications.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_plot_modifications_py.html)
    
  * Week 7: [June 25 - June 29]
    * Work on visualization_volume_rendering module
    * [volume_rendering.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_volume_rendering_py.html), 
[blenders.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_blenders_py.html), 
[off_axis_projection.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_off_axis_projection_py.html), 
[zbuffer_array.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_zbuffer_array_py.html), 
[create_spline.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_create_spline_py.html), 
[glfw_inputhook.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_glfw_inputhook_py.html), 
[utils.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_utils_py.html), 
[interactive_vr_helpers.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_interactive_vr_helpers_py.html), 
[image_handling.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_image_handling_py.html), 
[transfer_function_helper.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_transfer_function_helper_py.html), 
[camera_path.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_camera_path_py.html), 
[camera.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_camera_py.html), 
[lens.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_lens_py.html), 
[scene.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_scene_py.html), 
[transfer_functions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_transfer_functions_py.html), 
[render_source.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_render_source_py.html), 
[old_camera.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_visualization_volume_rendering_old_camera_py.html)
    
  * Week 8: [July 2 - July 6]
    * Work on utilities module
    * [hierarchy_inspection.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_hierarchy_inspection_py.html), 
[chemical_formulas.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_chemical_formulas_py.html), 
[orientation.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_orientation_py.html), 
[particle_generator.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_particle_generator_py.html), 
[png_writer.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_png_writer_py.html), 
[controller_system.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_parallel_tools_controller_system_py.html), 
[metadata.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_metadata_py.html), 
[utilities.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_frontends_ytdata_utilities_py.html), 
[nodal_data_utils.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_nodal_data_utils_py.html), 
[periodic_table.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_periodic_table_py.html), 
[amr_kdtools.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_amr_kdtree_amr_kdtools_py.html), 
[file_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_file_handler_py.html), 
[initial_conditions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_initial_conditions_py.html), 
[linear_interpolators.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_linear_interpolators_py.html), 
[writer.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_grid_data_format_writer_py.html), 
[configure.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_configure_py.html), 
[flagging_methods.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_flagging_methods_py.html), 
[logger.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_logger_py.html), 
[performance_counters.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_performance_counters_py.html), 
[mesh_code_generation.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_mesh_code_generation_py.html), 
[libconfig.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_pyparselibconfig_libconfig_py.html), 
[cosmology.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_cosmology_py.html), 
[rpdb.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_rpdb_py.html), 
[io_handler.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_io_handler_py.html), 
[fortran_utils.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_fortran_utils_py.html), 
[io_runner.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_parallel_tools_io_runner_py.html), 
[task_queue.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_parallel_tools_task_queue_py.html), 
[parameter_file_storage.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_parameter_file_storage_py.html), 
[encode.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_poster_encode_py.html), 
[math_utils.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_math_utils_py.html), 
[on_demand_imports.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_on_demand_imports_py.html), 
[amr_kdtree.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_amr_kdtree_amr_kdtree_py.html), 
[minimal_representation.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_minimal_representation_py.html), 
[conversion_athena.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_grid_data_format_conversion_conversion_athena_py.html), 
[exceptions.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_exceptions_py.html), 
[parallel_analysis_interface.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_parallel_tools_parallel_analysis_interface_py.html), 
[sdf.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_sdf_py.html), 
[command_line.py](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/yt_utilities_command_line_py.html)
    

* Week 9: [July 9 - July 13]
    * Work on Cython utilities module
    * allocation_container.pyx, alt_ray_tracers.pyx, amr_kdtools.pyx, autogenerated_element_samplers.pyx, basic_octree.pyx, bitarray.pyx, bounding_volume_hierarchy.pyx, contour_finding.pyx, cosmology_time.pyx, depth_first_octree.pyx, distance_queue.pyx, element_mappings.pyx, fnv_hash.pyx, fortran_reader.pyx, geometry_utils.pyx, grid_traversal.pyx, image_samplers.pyx, image_utilities.pyx, interpolators.pyx, lenses.pyx, line_integral_convolution.pyx, marching_cubes.pyx, mesh_triangulation.pyx, mesh_utilities.pyx, misc_utilities.pyx, origami.pyx, particle_mesh_operations.pyx, partitioned_grid.pyx, pixelization_routines.pyx, points_in_volume.pyx, primitives.pyx, quad_tree.pyx, ragged_arrays.pyx, write_array.pyx
    
### **Final Week**

  * Week 10: [July 16 - July 20]
    * Buffer Week (Phase 1 or 2 tasks overflow)
  * Week 11: [July 23 - July 27]
    * Buffer Week (Phase 1 or 2 tasks overflow)
  * Week 12: [July 30 - August 3]
    * Buffer Week (Phase 1 or 2 tasks overflow)
    * Wrap up all the code development
    * Finish the final report to be submitted

### **Students Submit Code and Final Evaluations**
  
  * Week 13: [August 6 - August 14]
    * Submit the overall code contribution and reports to GSoC
  

## Development Experience

I have 3 years 3 months of work experience as a software developer engineer at various technology [companies](#3-work-experience). During these years, I worked on many different technologies ranging from Java, Python, C/C++/C#, ASP.NET, SQL to JavaScript, jQuery, HTML/CSS and learned many frameworks. This experience has not only made me a better developer but also taught me to reach for coding excellence. It exposed me to the skills that make a developer complete like working in global teams, timely delivery of production code in large codebases and taking product ownership. Thus, I feel I am competent enough to work on this project.

As part of my coursework in [Neural Networks](https://compsci682.github.io/), I implemented deep learning library in python. For the course [Applied Information Retrieval](http://ciir.cs.umass.edu/~dfisher/cs546/syllabus.html), I created a toy search engine in Java that could efficiently index the corpus and return ranked query results. This semester in [AI](http://rbr.cs.umass.edu/shlomo/classes/683/index.html) I am implementing search algorithms like A*, constraint satisfaction problems, minimax adversarial search and others. In [Intelligent Visual Computing](https://people.cs.umass.edu/~kalo/courses/visual_computing/index.html) I am writing Matlab code for 3D shape reconstruction, deepnet eye detector, and marching cubes. Furthermore, I am implementing a simulator based [robot](http://www-robotics.cs.umass.edu/~grupen/603/handouts/syllabus.html) in C with 9 degrees of freedom. Since all these projects are forbidden to be shared publicly, I do not have them on GitHub. (I will be required to take professors' permission, in case I need to send it privately to the reviewers.)    

yt is my first open source community and thus I do not have any contributions that are publicly available. However, I have made 2 PRs for yt till now fixing issues [#1680](https://github.com/yt-project/yt/issues/1680) and [#1599](https://github.com/yt-project/yt/pull/1705).

## Why this project?

For any software product with increasing userbase, stability and reliability are utmost. Furthermore, no organization wants to ship more features at the cost of robustness. This makes effective test suite one of the most important backbones of any product. Better testing framework not only leads to early bug detection but also contributes to rapid code development. At present, the code coverage of yt is only one-third and there is a lot of scope to improve the runtime which makes me highly interested in this project.

Increasing code coverage at the same time reducing proportional test runtime makes this an interesting problem. This demands highly optimal test code and intelligent handcrafting of test cases. This makes it a challenging problem requiring extensive analysis and well-thought-out solution.

Furthermore, this project touches the entire codebase of yt. It would not only help me understand yt better but would also make me competent to keep contributing in future. I believe when one knows the end-to-end of a system, then one can think of innovative and polished solutions of the shortcomings in the system.

Given an opportunity to work on this project, I am confident with my possessed skills and the right mentorship, that I will be able to enhance yt making its developer more confident and at the same time increasing the penchant of its userbase. I believe this would be a wonderful learning opportunity for me and would be my pleasure to give back to the open source community.

## References
I thank [Nathan Goldbaum](https://github.com/ngoldbaum) and [Colin Marc](https://github.com/colinmarc) for their valuable feedbacks on this project proposal. I appreciate their help and efforts!


## Appendix
### 1. Personal Details
  * Name: Abhishek Singh
  * Current Degree Program: Master's in Computer Science
  * Current University: [University of Massachusetts, Amherst](https://www.umass.edu/)
  * Past Degree Program: Master's in Mathematics, Bachelor of Engineering in Computer Science
  * Past University: [Birla Institute of Technology and Science, Pilani](http://www.bits-pilani.ac.in/pilani/)
  * Phone: +1 413-824-1385
  * Email/Google Hangout: abhisheksing@umass.edu
  * GitHub: https://github.com/git-abhishek
  * LinkedIn: https://www.linkedin.com/in/asingh690/
  * [Resume](https://github.com/git-abhishek/gsoc-2018/blob/master/Abhishek_Singh_Resume.pdf)
  
### 2. Relevant Programming Skills
  * Python, Java, C/C++
  * Git and GitHub
  * Cython (Beginner)
  
### 3. Work Experience
  * Software Robotics Corp. (Junior Platform Engineer, 4 months)
  * Citi Bank (Application Developer Supervisor, 30 months)
  * Amazon (SDE Intern, 5 months)
   
### 4. Summer'18 Commitment
  * Except for GSoC, I do not have any other engagements for the summer. I will be working fulltime (40 hrs/week) on this project.
  
