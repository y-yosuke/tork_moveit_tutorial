cmake_minimum_required(VERSION 2.8.3)
project(tork_moveit_tutorial)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS)

###################################
## catkin specific configuration ##
###################################
catkin_package()

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
catkin_python_setup()

#############
## Install ##
#############

install(DIRECTORY script
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  USE_SOURCE_PERMISSIONS
)


#############
## Testing ##
#############

# make docs
# ROS_PARALLEL_JOBS='' catkin_make --pkg tork_moveit_tutorial -- docbuild
add_custom_target(docbuild_${PROJECT_NAME}
  COMMAND cmake -E remove_directory output
  COMMAND cmake -E make_directory output
  COMMAND sphinx-build -b latex doc output
  COMMAND cmake -E copy doc/by-nc-sa.png output/by-nc-sa.png
  #COMMAND cmake -E chdir output sh -c 'ls *.png | xargs extractbb'
  COMMAND cd output \\; ls *.png | xargs extractbb
  COMMAND sed -i 's@\\\\setcounter{tocdepth}{0}@@' output/${PROJECT_NAME}.tex
  COMMAND sed -i 's@\\\\end{document}@\\\\newpage\\\\input{../doc/cc.tex}\\\\end{document}@' output/${PROJECT_NAME}.tex
  COMMAND cmake -E chdir output platex ${PROJECT_NAME}.tex
  COMMAND cmake -E chdir output platex ${PROJECT_NAME}.tex
  COMMAND cmake -E chdir output dvipdfmx ${PROJECT_NAME}
  COMMAND cmake -E rename output/${PROJECT_NAME}.pdf output/${PROJECT_NAME}-${${PROJECT_NAME}_VERSION}.pdf
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
  )
