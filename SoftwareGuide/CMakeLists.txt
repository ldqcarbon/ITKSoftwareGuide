#####################################################
#
#   This is the root SoftwareGuide CMakeLists file.
#
#####################################################
#
# To Build the ITK Software Guide,
#
# Compile and build the ITK binaries. You will need to build the Examples in
# the Insight directory with the CMakeLists.txt file in ITK/
#
# Run this CMakeLists.txt file and specify the
# directory containing the Insight executables. Specify ITK data paths.
# The default data path inferred is ITK/Examples/Data. This should suffice
# for building the SoftwareGuide. Should more paths be need add them in a colon
# seperated list.
#
# Make
#
# More than one make may be necessary to ensure generation of the index
#
# The examples source files are parsed for
# command line argument tags and if found executed to generate output
# images, if not the images are expected to be present in the Art/ folder
# in PNG/XFIG/JPEG/EPS format.
#
cmake_minimum_required(VERSION 2.8.10)

project(SoftwareGuide C)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMake ${CMAKE_MODULE_PATH})

find_package(ITK 4 REQUIRED)
include(${ITK_USE_FILE})
find_path(ITK_EXECUTABLES_DIR
  NAMES ImageReadWrite ImageReadWrite.exe
  PATHS
  "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}"
  "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/Release"
  "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/Debug"
  "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/RelWithDebInfo"
  "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/MinSizeRel"
  DOC "Where the ITK examples executables are"
  )

set(PDF_QUALITY_LEVEL "Screen" CACHE STRING "PDF Quality. Options are: Screen, Printer, PrePress")
#
# Options for PDF generation.
# resolution in dpi.
#
#  /screen   = low    resolution = Distiller "Screen" optimized
#  /ebook    = medium resolution = Distiller "eBook"
#  /printer  = high   resolution = Distiller "Print" optimized
#  /prepress = high   resolution = Distiller like "Print" with some error checking differences
#
if( PDF_QUALITY_LEVEL STREQUAL "Screen" OR PDF_QUALITY_LEVEL STREQUAL "" )
  set(PS2PDF_CONVERTER_OPTIONS "-dPDFSETTINGS${PS2PDF_CONVERTER_VAL_DELIM}/screen" -r600)
  set(SoftwareGuide_PRINTED_VERSION_LATEX_BOOLEAN "\\itkPrintedVersionfalse")
endif()

if( PDF_QUALITY_LEVEL STREQUAL "Printer" )
  set(PS2PDF_CONVERTER_OPTIONS "-dPDFSETTINGS${PS2PDF_CONVERTER_VAL_DELIM}/printer" -dUseCIEColor -r600)
  set(SoftwareGuide_PRINTED_VERSION_LATEX_BOOLEAN "\\itkPrintedVersiontrue")
endif()

if( PDF_QUALITY_LEVEL STREQUAL "PrePress" )
  set(PS2PDF_CONVERTER_OPTIONS "-dPDFSETTINGS${PS2PDF_CONVERTER_VAL_DELIM}/prepress" "-dColorImageResolution${PS2PDF_CONVERTER_VAL_DELIM}600" "-dGrayImageResolution${PS2PDF_CONVERTER_VAL_DELIM}600" -r600)
  set(SoftwareGuide_PRINTED_VERSION_LATEX_BOOLEAN "\\itkPrintedVersiontrue")
endif()

option(DRAFT_WATERMARK "Add a draft watermark to the output." ON)
if(DRAFT_WATERMARK)
  set(SoftwareGuide_DRAFT_WATERMARK_LATEX_BOOLEAN "\\itkDraftWatermarktrue")
else()
  set(SoftwareGuide_DRAFT_WATERMARK_LATEX_BOOLEAN "\\itkDraftWatermarkfalse")
endif()

#
# Generate .cmake files containing those macros defined above.
#
# First make a directory to store generated images
set(ART_GENERATED_FOLDER "${SoftwareGuide_BINARY_DIR}/Art/Generated" )
make_directory( "${ART_GENERATED_FOLDER}" )

#
# Rebuild the Software Guide figures
set(RUN_EXAMPLES_SCRIPT "${SoftwareGuide_SOURCE_DIR}/Examples/RunExamples.py" CACHE FILEPATH "Where the RunExamples Script is")


#
# Find ImageMagick tools
# This is used to convert image formats
#
#
# Find ImageMagick tools
# This is used to convert image formats
#
#include(${CMAKE_ROOT}/Modules/FindImageMagick.cmake)
find_package(ImageMagick REQUIRED)
if(NOT IMAGEMAGICK_CONVERT_EXECUTABLE)
  message("ImageMagick convert utility was not found. Please pass to advanced mode and provide its full path")
endif(NOT IMAGEMAGICK_CONVERT_EXECUTABLE)


#
# ADD THE LIST OF INPUT IMAGES YOU WANT FLIPPED HERE.
#
# NOTE:
# All input images in the list and all outputs that use these images
# as inputs in cmd line args will be flipped about 'y'. The command line
# argument itself does not run using the flipped images; instead the eps
# images corresponding to the inputs mentioned in the list and their output
# dependencies are flipped for inclusion in the TEX document.

option(ITK_FLIP_INPUTS_AND_THEIR_OUTPUTS "Flip the input images specified in CMakeLists and their corresponding outputs" ON)
set(ITK_FLIP_IMG
  BrainProtonDensitySlice
  BrainT1Slice
  BrainT1SliceBorder20
  BrainProtonDensitySliceBorder20
  BrainProtonDensitySliceRotated10
  BrainProtonDensitySliceR10X13Y17
  BrainProtonDensitySliceR10X13Y17S12
  ConfidenceConnectedOutput1
  ConfidenceConnectedOutput2
  ConfidenceConnectedOutput3
  VisibleWomanEyeSlice
  VisibleWomanHeadSlice
  BinaryImage
  VentricleModel
  FivePoints
  ThresholdSegmentationLevelSetImageFilterVentricle
  BrainProtonDensitySliceShifted13x17y
  IsolatedConnectedImageFilterOutput1
  OtsuThresholdImageFilterOutput
  FastMarchingFilterOutput1
  FastMarchingFilterOutput2
  FastMarchingFilterOutput3
  ShapeDetectionLevelSetFilterOutput1
  ShapeDetectionLevelSetFilterOutput2
  ShapeDetectionLevelSetFilterOutput3
  ThresholdSegmentationLevelSetImageFilterWhiteMatter
  ThresholdSegmentationLevelSetImageFilterVentricle
  ThresholdSegmentationLevelSetImageFilterGrayMatter
  BinaryThresholdImageFilterOutput
  BinaryMedianImageFilterOutput
  SigmoidImageFilterOutput
  GradientMagnitudeImageFilterOutput
  GradientMagnitudeRecursiveGaussianImageFilterOutput3
  GradientMagnitudeRecursiveGaussianImageFilterOutput5
  DerivativeImageFilterOutput
  MeanImageFilterOutput
  MedianImageFilterOutput
  MathematicalMorphologyGrayscaleErosionOutput
  MathematicalMorphologyGrayscaleDilationOutput
  DiscreteGaussianImageFilterOutput
  BinomialBlurImageFilterOutput
  GradientAnisotropicDiffusionImageFilterOutput
  ThresholdImageFilterOutputBelow
  ThresholdImageFilterOutputAbove
  ThresholdImageFilterOutputOutside
  LaplacianRecursiveGaussianImageFilterOutput3
  LaplacianRecursiveGaussianImageFilterOutput5
  MathematicalMorphologyGrayscaleErosionOutput
  MathematicalMorphologyGrayscaleDilationOutput
  MathematicalMorphologyBinaryErosionOutput
  MathematicalMorphologyBinaryDilationOutput
  VotingBinaryHoleFillingImageFilterOutput1
  VotingBinaryHoleFillingImageFilterOutput2
  VotingBinaryHoleFillingImageFilterOutput3
  VotingBinaryIterativeHoleFillingImageFilterOutput1
  VotingBinaryIterativeHoleFillingImageFilterOutput2
  VotingBinaryIterativeHoleFillingImageFilterOutput3
  SmoothingRecursiveGaussianImageFilterOutput3
  SmoothingRecursiveGaussianImageFilterOutput5
  CurvatureAnisotropicDiffusionImageFilterOutput
  CurvatureFlowImageFilterOutput
  MinMaxCurvatureFlowImageFilterOutput
  BilateralImageFilterOutput
  RGBGradientAnisotropicDiffusionImageFilterOutput
  RGBCurvatureAnisotropicDiffusionImageFilterOutput
  ResampleImageFilterOutput1
  ResampleImageFilterOutput2
  ResampleImageFilterOutput10
  ImageRegistration1Output
  ImageRegistration1DifferenceBefore
  ImageRegistration1DifferenceAfter
  ImageRegistration2Output
  ImageRegistration2CheckerboardBefore
  ImageRegistration2CheckerboardAfter
  ImageRegistration5Output
  ImageRegistration5DifferenceBefore
  ImageRegistration5DifferenceAfter
  ImageRegistration5Output2
  ImageRegistration5DifferenceBefore2
  ImageRegistration5DifferenceAfter2
  ImageRegistration6Output
  ImageRegistration6DifferenceBefore
  ImageRegistration6DifferenceAfter
  ImageRegistration7Output
  ImageRegistration7DifferenceBefore
  ImageRegistration7DifferenceAfter
  ImageRegistration8Output
  ImageRegistration8DifferenceBefore
  ImageRegistration8DifferenceAfter
  ImageRegistration9Output
  ImageRegistration9DifferenceBefore
  ImageRegistration9DifferenceAfter
  MultiResImageRegistration1Output
  MultiResImageRegistration1CheckerboardBefore
  MultiResImageRegistration1CheckerboardAfter
  MultiResImageRegistration2Output
  MultiResImageRegistration2CheckerboardBefore
  MultiResImageRegistration2CheckerboardAfter
  ConnectedThresholdOutput1
  ConnectedThresholdOutput2
  ConnectedThresholdOutput3
  FastMarchingImageFilterOutput5
  FastMarchingImageFilterOutput6
  FastMarchingImageFilterOutput7
  FastMarchingImageFilterOutput8
  ShapeDetectionLevelSetFilterOutput5
  ShapeDetectionLevelSetFilterOutput6
  ShapeDetectionLevelSetFilterOutput7
  ShapeDetectionLevelSetFilterOutput8
  GeodesicActiveContourImageFilterOutput1
  GeodesicActiveContourImageFilterOutput2
  GeodesicActiveContourImageFilterOutput3
  GeodesicActiveContourImageFilterOutput5
  GeodesicActiveContourImageFilterOutput6
  GeodesicActiveContourImageFilterOutput7
  GeodesicActiveContourImageFilterOutput8
  CannySegmentationLevelSetImageFilterVentricle1
  LaplacianSegmentationLevelSetImageFilterVentricle
  BrainT1Slice_labelled
  ScalarImageMarkovRandomField1Output
  ImageSliceIteratorWithIndexOutput
  NeighborhoodIterators1a
  NeighborhoodIterators4a
  NeighborhoodIterators4b
  NeighborhoodIterators4c
  NeighborhoodIterators4d
  ShapedNeighborhoodIterators1b
  ImageAdaptorThresholdingA
  ImageAdaptorThresholdingB
  ImageRegistration4Output
  ImageRegistration4CheckerboardBefore
  ImageRegistration4CheckerboardAfter
  MultiStageImageRegistration1Output
  MultiStageImageRegistration1CheckerboardBefore
  MultiStageImageRegistration1CheckerboardAfter
  MultiStageImageRegistration2Output
  MultiStageImageRegistration2CheckerboardBefore
  MultiStageImageRegistration2CheckerboardAfter
  ImageRegionIteratorOutput
)
# END FLIP_INPUTS LIST

# Convert an image from some file format to EPS for inclusion in Latex using
# ImageMagick. This image is an input image. A separate macro is necessary
# because input images do not have any dependecies
set(EPS_INPUT_CONVERT_FLAGS)
set(EPS_OUTPUT_CONVERT_FLAGS)
if( PDF_QUALITY_LEVEL STREQUAL "Printer" )
  # Upsample to satify the printer
  set( EPS_INPUT_CONVERT_FLAGS -density 72 )
  set( EPS_OUTPUT_CONVERT_FLAGS -density 72 -scale 4194304@ )
endif()
macro(CONVERT_INPUT_IMG SOME_IMG EPS_IMG IMAGEMAGICK_FLAGS)
  get_filename_component(IMG_BASENAME ${SOME_IMG} NAME_WE)
  list(FIND ITK_FLIP_IMG "${IMG_BASENAME}" _index)
  if(${_index} GREATER -1)
    if(NOT "${FLAGS}" MATCHES "-flip")
      set(FLAGS ${FLAGS} -flip)
    endif()
  endif()
  if( NOT DEFINED ${EPS_IMG}_HAS_CUSTOM_COMMAND)
    add_custom_command(
        OUTPUT      "${EPS_IMG}"
        COMMAND     ${IMAGEMAGICK_CONVERT_EXECUTABLE}
        ARGS        ${FLAGS} -quality 100  ${EPS_INPUT_CONVERT_FLAGS} "${SOME_IMG}" ${EPS_OUTPUT_CONVERT_FLAGS} "${EPS_IMG}"
        DEPENDS     ${RUN_EXAMPLES_SCRIPT}
    )
    set(${EPS_IMG}_HAS_CUSTOM_COMMAND 1)
  endif()
  set(FLAGS "")
endmacro(CONVERT_INPUT_IMG)

#
# Find Python executable
include(${CMAKE_ROOT}/Modules/FindPythonInterp.cmake)
if( NOT PYTHONINTERP_FOUND )
  message(FATAL_ERROR "Python executable was not found")
endif( NOT PYTHONINTERP_FOUND )
set(PYTHONCXXPARSER ${SoftwareGuide_SOURCE_DIR}/ParseCxxExamples.py)
if( NOT EXISTS ${PYTHONCXXPARSER} )
  message(FATAL_ERROR "Missing script: ${SoftwareGuide_SOURCE_DIR}/ParseCxxExamples.py")
endif()

#
# Search for all the example sources
#
file( GLOB_RECURSE all_sources  ${ITK_SOURCE_DIR}/Examples/*.cxx)
# Exclude the RegistrationITKv3 sources.
set( ITK_EXAMPLES_SRCS )
foreach( source ${all_sources} )
  if( ITKv3_COMPATIBILITY )
    set(exclude RegistrationITKv4)
  else()
    set(exclude RegistrationITKv3)
  endif()
  if( NOT "${source}" MATCHES "${exclude}" )
    list( APPEND ITK_EXAMPLES_SRCS "${source}" )
  endif()
endforeach()

message(STATUS "PRE_RUN_PYTHON ART GENERATION.")
message(STATUS "Running: ${PYTHON_EXECUTABLE} ${RUN_EXAMPLES_SCRIPT} --itkSource ${ITK_SOURCE_DIR} --itkBuildDir ${ITK_BINARY_DIR} --itkExecDir ${ITK_EXECUTABLES_DIR} --SWGuidBaseOutput ${SoftwareGuide_BINARY_DIR} "
  )
execute_process(
  COMMAND ${PYTHON_EXECUTABLE}
  ${RUN_EXAMPLES_SCRIPT}
  --itkSource ${ITK_SOURCE_DIR}
  --itkBuildDir ${ITK_BINARY_DIR}
  --itkExecDir ${ITK_EXECUTABLES_DIR}
  --SWGuidBaseOutput ${SoftwareGuide_BINARY_DIR}
  WORKING_DIRECTORY "${ART_GENERATED_FOLDER}"
  RESULT_VARIABLE    RUN_EXAMPLES_RESULT
    )
if( NOT ${RUN_EXAMPLES_RESULT} EQUAL 0)
  message(FATAL_ERROR "POST_RUN_PYTHON ART GENERATION ${RUN_EXAMPLES_RESULT}.")
endif()

include(${SoftwareGuide_BINARY_DIR}/Examples/GeneratedDependancies.cmake)

#
# Parse Latex file for latex includes
#
set(TEX_DEPENDENCIES "")
foreach(example ${ITK_EXAMPLES_SRCS})
  get_filename_component(TEX_FILE_BASE ${example} NAME_WE)
  set(TEX_FILE ${SoftwareGuide_BINARY_DIR}/Examples/${TEX_FILE_BASE}.tex)
  add_custom_command(
    OUTPUT    ${TEX_FILE}
    COMMAND   ${PYTHON_EXECUTABLE}
    ARGS      ${PYTHONCXXPARSER} ${example} ${TEX_FILE}
    DEPENDS   ${PYTHONCXXPARSER} ${example} ${${TEX_FILE_BASE}-DEPS}
  )
  set(TEX_DEPENDENCIES ${TEX_DEPENDENCIES} ${TEX_FILE})
endforeach(example)

add_custom_target(BuildTexFiles ALL
  DEPENDS ${TEX_DEPENDENCIES}
  )


add_subdirectory(Examples)
add_subdirectory(Art)
add_subdirectory(Latex)
#
# Examples Directory is where .cxx files are converted
# into .tex by a python script.
#
# Art Directory with images and diagrams
#
# Latex Directory with tex files
#
