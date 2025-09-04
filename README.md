# system_design

<div className="min-h-screen ">
      <TemplatePreviewModal
        isOpen={!!selectedTemplate || isLoadingModal}
        onClose={closeModal}
        template={selectedTemplate}
        isLoading={isLoadingModal}
        error={modalError}
        favoriteLoading={favoriteLoading}
        setFavoriteLoading={setFavoriteLoading}
        toggleFavorite={toggleFavorite}
        isFavorited={isFavorited}
      />
      <CreateTemplateButton />

      <PageHeader
        view={view}
        setView={setView}
        showMobileFilters={showMobileFilters}
        setShowMobileFilters={setShowMobileFilters}
        title="All Templates"
        actionButton={
          <div className="flex items-center space-x-2">
            <Link
              href="/user/templates/favorites"
              className="px-3 py-1 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors text-sm flex items-center space-x-1"
              title="View favorite templates"
            >
              <Heart className="h-4 w-4 fill-current" />
              <span>Favorites ({favoriteIds.size})</span>
            </Link>
            <button
              onClick={() => fetchTemplates()}
              disabled={loading}
              className="px-3 py-1 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors disabled:opacity-50 disabled:cursor-not-allowed text-sm"
              title="Refresh templates"
            >
              {loading ? "Loading..." : "Refresh"}
            </button>
          </div>
        }
      />

      <div className="hidden md:block bg-white border-b px-3 py-2 rounded-lg">
        <div className="flex items-end justify-between gap-4">
          <div className="flex items-end space-x-2">
            {/* Industry Dropdown */}
            <div className="flex flex-col">
              <Label className="text-sm font-normal text-gray-500 mb-1.5">
                Industry:
              </Label>
              <DropdownMenu>
                <DropdownMenuTrigger asChild>
                  <Button
                    variant="outline"
                    className="w-48 lg:w-40 xl:w-36 2xl:w-48 h-9 px-3 py-2 border font-thin border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between hover:bg-white hover:text-gray-400"
                  >
                    <span>
                      {selectedIndustries.length > 0
                        ? selectedIndustries.length === industryOptions.length
                          ? "All"
                          : `${selectedIndustries.length} Selected`
                        : "Select"}
                    </span>
                    <ChevronDown className="h-4 w-4 text-gray-600" />
                  </Button>
                </DropdownMenuTrigger>
                <DropdownMenuContent className="z-[70] w-48 lg:w-40 xl:w-36 2xl:w-48">
                  <CheckboxMenuItem
                    checked={isAllIndustriesSelected}
                    onCheckedChange={() => handleIndustryToggle("All")}
                  >
                    Select All
                  </CheckboxMenuItem>
                  <DropdownMenuSeparator />
                  {industryOptions.map((industry) => (
                    <CheckboxMenuItem
                      key={industry}
                      checked={selectedIndustries.includes(industry)}
                      onCheckedChange={() => handleIndustryToggle(industry)}
                    >
                      <span className="whitespace-nowrap overflow-hidden text-ellipsis max-w-xs block align-middle">
                        {industry}
                      </span>
                    </CheckboxMenuItem>
                  ))}
                </DropdownMenuContent>
              </DropdownMenu>
            </div>

            {/* Document Dropdown */}
            <div className="flex flex-col">
              <Label className="text-sm font-normal text-gray-500 mb-1.5">
                Document:
              </Label>
              <DropdownMenu>
                <DropdownMenuTrigger asChild>
                  <Button
                    variant="outline"
                    className="w-48 lg:w-40 xl:w-36 2xl:w-48 h-9 px-3 font-thin py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between hover:bg-white hover:text-gray-400"
                  >
                    <span>
                      {selectedDocuments.length > 0
                        ? selectedDocuments.length === documentOptions.length
                          ? "All"
                          : `${selectedDocuments.length} Selected`
                        : "Select"}
                    </span>
                    <ChevronDown className="h-4 w-4 text-gray-400" />
                  </Button>
                </DropdownMenuTrigger>
                <DropdownMenuContent className="z-[70] w-48 lg:w-40 xl:w-36 2xl:w-48">
                  <CheckboxMenuItem
                    checked={isAllDocumentsSelected}
                    onCheckedChange={() => handleDocumentToggle("All")}
                  >
                    All
                  </CheckboxMenuItem>
                  <DropdownMenuSeparator />
                  {documentOptions.map((docType) => (
                    <CheckboxMenuItem
                      key={docType}
                      checked={selectedDocuments.includes(docType)}
                      onCheckedChange={() => handleDocumentToggle(docType)}
                    >
                      {/* {docType} */}
                      <span className="whitespace-nowrap overflow-hidden text-ellipsis max-w-xs block align-middle">
                        {docType}
                      </span>
                    </CheckboxMenuItem>
                  ))}
                </DropdownMenuContent>
              </DropdownMenu>
            </div>

            <div className="flex flex-col">
              <Label className="text-sm font-normal text-gray-500 mb-1.5 ">
                Created by:
              </Label>
              <DropdownMenu>
                <DropdownMenuTrigger asChild>
                  <Button
                    variant="outline"
                    className="w-48 lg:w-40 xl:w-36 2xl:w-48 h-9 px-3 font-thin py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between hover:bg-white hover:text-gray-400"
                  >
                    <span className="">
                      {selectedCreatedBy.length > 0
                        ? selectedCreatedBy.length === userProfiles.length
                          ? "All"
                          : `${selectedCreatedBy.length} Selected`
                        : "Select"}
                    </span>
                    <ChevronDown className="h-4 w-4 text-gray-600 flex-shrink-0" />
                  </Button>
                </DropdownMenuTrigger>
                <DropdownMenuContent className="z-[70] w-48 lg:w-40 xl:w-36 2xl:w-48">
                  {userProfiles
                    .sort((a, b) => {
                      if (currentUser && a.user_id === currentUser.id)
                        return -1;
                      if (currentUser && b.user_id === currentUser.id) return 1;
                      return 0;
                    })
                    .map((profile) => {
                      const displayName =
                        currentUser && profile.user_id === currentUser.id
                          ? "Created by Me"
                          : profile.name;
                      const isChecked = selectedCreatedBy.includes(displayName);

                      return (
                        <CheckboxMenuItem
                          key={profile.id}
                          checked={isChecked}
                          onCheckedChange={() =>
                            handleCreatedByOptionsToggle(displayName)
                          }
                        >
                          {/* {displayName} */}
                          <span className="whitespace-nowrap overflow-hidden text-ellipsis max-w-xs block align-middle">
                            {displayName}
                          </span>
                        </CheckboxMenuItem>
                      );
                    })}
                </DropdownMenuContent>
              </DropdownMenu>
            </div>
            <div className="flex flex-col relative min-w-0 flex-shrink-0">
              <Label
                htmlFor="dateRange"
                className="text-sm font-normal text-gray-500 mb-1.5 whitespace-nowrap"
              >
                Date Range:
              </Label>
              <div className="w-full">
                <Popover>
                  <PopoverTrigger asChild>
                    <Button
                      variant="outline"
                      className="w-auto min-w-fit h-9 px-3 py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm font-normal flex items-center gap-2"
                    >
                      <span className="whitespace-nowrap">
                        {formatDateRange(dateRange)}
                      </span>
                      <img
                        src="/icons/CalendarIconBlue.svg"
                        alt="Calendar Icon"
                        className="h-4 w-4 flex-shrink-0 lg:h-6 lg:w-6"
                      />
                    </Button>
                  </PopoverTrigger>
                  <PopoverContent className="z-[70] w-auto p-0" align="start">
                    <Calendar
                      initialFocus
                      mode="range"
                      defaultMonth={dateRange?.from}
                      selected={dateRange}
                      onSelect={setDateRange}
                      numberOfMonths={2}
                    />
                  </PopoverContent>
                </Popover>
              </div>
            </div>
          </div>

          <div className="relative flex-grow min-w-0 lg:min-w-48">
            <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
              <Search className="h-4 w-4 text-gray-400" />
            </div>
            <Input
              type="text"
              placeholder="Search"
              className="w-full pl-10 h-9 border-gray-300 rounded-md font-thin text-gray-400 min-w-0"
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
            />
          </div>
        </div>
      </div>

      {/* Mobile Search Bar */}
      <div className="md:hidden bg-white border-b px-4 py-3">
        <div className="relative">
          <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
            <Search className="h-4 w-4 text-gray-400" />
          </div>
          <Input
            type="text"
            placeholder="Search"
            className="w-full pl-10 h-9 border-gray-300 rounded-md border-2 text-gray-400 focus:outline-none focus:ring-0 focus:border-gray-300 focus:shadow-none"
            value={searchQuery}
            onChange={(e) => setSearchQuery(e.target.value)}
          />
        </div>
      </div>

      {/* Mobile Filters */}
      {showMobileFilters && (
        <div className="md:hidden bg-white border-b px-4 py-4 space-y-4">
          {/* Mobile Industry Dropdown */}
          <div className="flex flex-col">
            <Label className="text-sm font-normal text-gray-500 mb-1.5">
              Industry:
            </Label>
            <DropdownMenu>
              <DropdownMenuTrigger asChild>
                <Button
                  variant="outline"
                  className="w-full h-9 px-3 py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between font-thin hover:bg-white hover:text-gray-400"
                >
                  <span>
                    {selectedIndustries.length > 0
                      ? selectedIndustries.length === industryOptions.length
                        ? "All"
                        : `${selectedIndustries.length} Selected`
                      : "Select"}
                  </span>
                  <ChevronDown className="h-4 w-4 text-gray-600" />
                </Button>
              </DropdownMenuTrigger>
              <DropdownMenuContent className="z-[70] w-[var(--radix-dropdown-menu-trigger-width)]">
                <CheckboxMenuItem
                  checked={isAllIndustriesSelected}
                  onCheckedChange={() => handleIndustryToggle("All")}
                >
                  Select All
                </CheckboxMenuItem>
                <DropdownMenuSeparator />
                {industryOptions.map((industry) => (
                  <CheckboxMenuItem
                    key={industry}
                    checked={selectedIndustries.includes(industry)}
                    onCheckedChange={() => handleIndustryToggle(industry)}
                  >
                    {industry}
                  </CheckboxMenuItem>
                ))}
              </DropdownMenuContent>
            </DropdownMenu>
          </div>

          {/* Mobile Document Dropdown */}
          <div className="flex flex-col">
            <Label className="text-sm font-normal text-gray-500 mb-1.5">
              Document:
            </Label>
            <DropdownMenu>
              <DropdownMenuTrigger asChild>
                <Button
                  variant="outline"
                  className="w-full h-9 px-3 py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between font-thin hover:bg-white hover:text-gray-400"
                >
                  <span>
                    {selectedDocuments.length > 0
                      ? selectedDocuments.length === documentOptions.length
                        ? "All"
                        : `${selectedDocuments.length} Selected`
                      : "Select"}
                  </span>
                  <ChevronDown className="h-4 w-4 text-gray-600" />
                </Button>
              </DropdownMenuTrigger>
              <DropdownMenuContent className="z-[70] w-[var(--radix-dropdown-menu-trigger-width)]">
                <CheckboxMenuItem
                  checked={isAllDocumentsSelected}
                  onCheckedChange={() => handleDocumentToggle("All")}
                >
                  All
                </CheckboxMenuItem>
                <DropdownMenuSeparator />
                {documentOptions.map((docType) => (
                  <CheckboxMenuItem
                    key={docType}
                    checked={selectedDocuments.includes(docType)}
                    onCheckedChange={() => handleDocumentToggle(docType)}
                  >
                    {docType}
                  </CheckboxMenuItem>
                ))}
              </DropdownMenuContent>
            </DropdownMenu>
          </div>

          {/* Mobile Created by Dropdown */}
          <div className="flex flex-col">
            <Label className="text-sm font-normal text-gray-500 mb-1.5">
              Created by:
            </Label>
            <DropdownMenu>
              <DropdownMenuTrigger asChild>
                <Button
                  variant="outline"
                  className="w-full h-9 px-3 py-2 border border-gray-300 rounded-md bg-white text-gray-400 text-sm flex items-center justify-between font-thin hover:bg-white hover:text-gray-400"
                >
                  <span>
                    {selectedCreatedBy.length > 0
                      ? selectedCreatedBy.length === userProfiles.length
                        ? "All"
                        : `${selectedCreatedBy.length} Selected`
                      : "Select"}
                  </span>
                  <ChevronDown className="h-4 w-4 text-gray-600" />
                </Button>
              </DropdownMenuTrigger>
              <DropdownMenuContent className="z-[70] w-[var(--radix-dropdown-menu-trigger-width)]">
                {userProfiles
                  .sort((a, b) => {
                    // Put current user first
                    if (currentUser && a.user_id === currentUser.id) return -1;
                    if (currentUser && b.user_id === currentUser.id) return 1;
                    return 0;
                  })
                  .map((profile) => {
                    // Show "Created by Me" for the logged-in user, otherwise show their name
                    const displayName =
                      currentUser && profile.user_id === currentUser.id
                        ? "Created by Me"
                        : profile.name;
                    const isChecked = selectedCreatedBy.includes(displayName);

                    return (
                      <CheckboxMenuItem
                        key={profile.id}
                        checked={isChecked}
                        onCheckedChange={() =>
                          handleCreatedByOptionsToggle(displayName)
                        }
                      >
                        {displayName}
                      </CheckboxMenuItem>
                    );
                  })}
              </DropdownMenuContent>
            </DropdownMenu>
          </div>

          {/* Mobile Date Range Dropdown */}
          <div className="flex flex-col relative">
            <Label
              htmlFor="dateRange-mobile"
              className="text-sm font-normal text-gray-500 mb-1"
            >
              Date Range:
            </Label>
            {/* w-48 */}
            <div className="w-full">
              <Popover>
                <PopoverTrigger asChild>
                  <Button
                    variant="outline"
                    className="w-full h-8 px-3 py-2 border-2 border-gray-300 rounded-md bg-white text-gray-400 text-sm justify-between font-normal max-sm:h-10 max-sm:text-base"
                  >
                    <span className="max-sm:text-sm">
                      {formatDateRange(dateRange)}
                    </span>
                    <img
                      src="/icons/CalendarIconBlue.svg"
                      alt="Calendar"
                      className="h-5 w-5 max-sm:h-4 max-sm:w-4 ml-auto"
                    />
                  </Button>
                </PopoverTrigger>
                <PopoverContent
                  className="w-auto p-0 max-sm:w-[90vw] max-sm:max-w-sm"
                  align="center"
                >
                  <Calendar
                    initialFocus
                    mode="range"
                    defaultMonth={dateRange?.from}
                    selected={dateRange}
                    onSelect={setDateRange}
                    numberOfMonths={1}
                    className="max-sm:text-sm"
                    classNames={{
                      day: "h-9 w-9 text-center text-sm p-0 font-normal aria-selected:opacity-100 flex items-center justify-center",
                      day_today: "bg-accent text-accent-foreground",
                      day_selected:
                        "bg-primary text-primary-foreground hover:bg-primary hover:text-primary-foreground focus:bg-primary focus:text-primary-foreground",
                      day_range_middle:
                        "aria-selected:bg-accent aria-selected:text-accent-foreground",
                    }}
                  />
                </PopoverContent>
              </Popover>
            </div>
          </div>
        </div>
      )}

      <div className="p-4 md:p-6 md:px-0.5">
        {filteredTemplates.length === 0 ? (
          <div className="text-center py-16">
            <div className="text-gray-400 text-6xl mb-4">📄</div>
            <h3 className="text-lg font-semibold text-gray-800 mb-2">
              No templates found
            </h3>
            <p className="text-gray-500 mb-4">
              {searchQuery ||
              selectedIndustries.length > 0 ||
              selectedDocuments.length > 0 ||
              selectedCreatedBy.length > 0
                ? "Try adjusting your search criteria or filters"
                : "No templates available for your organization"}
            </p>
            {(searchQuery ||
              selectedIndustries.length > 0 ||
              selectedDocuments.length > 0 ||
              selectedCreatedBy.length > 0) && (
              <button
                onClick={() => {
                  setSearchQuery("");
                  setSelectedIndustries([]);
                  setSelectedDocuments([]);
                  setSelectedCreatedBy([]);
                  setDateRange({ from: null, to: null });
                }}
                className="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
              >
                Clear Filters
              </button>
            )}
          </div>
        ) : view === "grid" ? (
          <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-4 gap-4 md:gap-6">
            {filteredTemplates.map((template, index) => (
              <div
                key={template.id || index}
                className="flex flex-col w-full cursor-pointer"
                onMouseEnter={() => setHoveredTemplate(index)}
                onMouseLeave={() => setHoveredTemplate(null)}
                onClick={() => openModal(template)}
                role="button"
                tabIndex={0}
                onKeyDown={(e) => {
                  if (e.key === "Enter" || e.key === " ") {
                    openModal(template);
                  }
                }}
              >
                <div
                  className={`bg-white border rounded-xl overflow-hidden shadow-sm hover:shadow-md transition-shadow gap-2 ${
                    template.isUniversal
                      ? "border-purple-300 ring-2 ring-purple-100"
                      : "border-gray-200"
                  }`}
                >
                  {/* demo */}
                  <div className="relative z-0 isolate w-full h-56 md:h-80 overflow-hidden bg-gray-100">
                    {/* TOP BAR: left file icon | right: Universal + menu (same line) */}
                    <div className="absolute top-2 left-2 right-2 z-20 flex items-center justify-between pointer-events-none">
                      {/* Left: file-type icon */}
                      <div className="pointer-events-auto">
                        <Image
                          src={getFileTypeIcon(template)}
                          alt="file type"
                          width={35}
                          height={35}
                          className="rounded"
                        />
                      </div>

                      {/* Right: Universal pill + 3-dots menu */}
                      <div
                        className="relative flex items-center gap-2 pointer-events-auto"
                        ref={(el) => (menuRefs.current[index] = el)}
                      >
                        {template.isUniversal && (
                          <div className="bg-purple-600 text-white px-2 py-1 rounded-full text-xs font-semibold flex items-center space-x-1">
                            <span>🌐</span>
                            <span>Universal</span>
                          </div>
                        )}

                        <button
                          className="p-1 rounded-full bg-gray-100 backdrop-blur-sm transition-colors"
                          onClick={(e) => {
                            e.stopPropagation();
                            toggleMenu(index, e);
                          }}
                        >
                          <MoreVertical className="h-4 w-4" />
                        </button>

                        {activeMenu === index && (
                          <div className="absolute right-0 top-full mt-1 w-30 bg-white border border-gray-200 rounded-lg shadow-lg z-[70]">
                            <div className="py-1">
                              <button
                                onClick={(e) => {
                                  e.stopPropagation();
                                  handleTemplateAction("use", template);
                                }}
                                className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors"
                              >
                                <FileText className="h-4 w-4 mr-2 text-gray-700" />
                                <span className="text-gray-400 text-xs">
                                  Use Template
                                </span>
                              </button>

                              {!template.isUniversal && (
                                <>
                                  <button
                                    onClick={(e) => {
                                      e.stopPropagation();
                                      handleTemplateAction("edit", template);
                                    }}
                                    className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors"
                                  >
                                    <Edit className="h-4 w-4 mr-2 text-gray-700" />
                                    <span className="text-gray-400 text-xs">
                                      Edit
                                    </span>
                                  </button>

                                  <button
                                    onClick={(e) => {
                                      e.stopPropagation();
                                      handleTemplateAction(
                                        "favorite",
                                        template
                                      );
                                    }}
                                    disabled={favoriteLoading === template.id}
                                    className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                                  >
                                    {favoriteLoading === template.id ? (
                                      <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                                    ) : (
                                      <Heart
                                        className={`h-4 w-4 mr-2 ${
                                          isFavorited(template.id)
                                            ? "text-red-500 fill-current"
                                            : "text-gray-700"
                                        }`}
                                      />
                                    )}
                                    <span className="text-gray-400 text-xs">
                                      {favoriteLoading === template.id
                                        ? "Updating..."
                                        : isFavorited(template.id)
                                        ? "Unfavorite"
                                        : "Favorite"}
                                    </span>
                                  </button>

                                  <button
                                    onClick={(e) => {
                                      e.stopPropagation();
                                      handleTemplateAction("delete", template);
                                    }}
                                    disabled={deleteLoading === template.id}
                                    className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                                  >
                                    {deleteLoading === template.id ? (
                                      <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                                    ) : (
                                      <Trash2 className="h-4 w-4 mr-2 text-gray-700" />
                                    )}
                                    <span className="text-gray-400 text-xs">
                                      {deleteLoading === template.id
                                        ? "Deleting..."
                                        : "Delete"}
                                    </span>
                                  </button>

                                  <button
                                    onClick={(e) => {
                                      e.stopPropagation();
                                      handleTemplateAction(
                                        "duplicate",
                                        template
                                      );
                                    }}
                                    disabled={duplicateLoading === template.id}
                                    className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                                  >
                                    {duplicateLoading === template.id ? (
                                      <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                                    ) : (
                                      <Copy className="h-4 w-4 mr-2 text-gray-700" />
                                    )}
                                    <span className="text-gray-400 text-xs">
                                      {duplicateLoading === template.id
                                        ? "Duplicating..."
                                        : "Duplicate"}
                                    </span>
                                  </button>

                                  <button
                                    onClick={(e) => {
                                      e.stopPropagation();
                                      handleTemplateAction("archive", template);
                                    }}
                                    disabled={archiveLoading === template.id}
                                    className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                                  >
                                    {archiveLoading === template.id ? (
                                      <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                                    ) : (
                                      <Archive className="h-4 w-4 mr-2 text-gray-700" />
                                    )}
                                    <span className="text-gray-400 text-xs">
                                      {archiveLoading === template.id
                                        ? "Archiving..."
                                        : "Archive"}
                                    </span>
                                  </button>
                                </>
                              )}

                              <button
                                onClick={(e) => {
                                  e.stopPropagation();
                                  handleTemplateAction("download", template);
                                }}
                                disabled={downloadLoading === template.id}
                                className="flex items-center w-full px-3 py-2 text-xs text-gray-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                              >
                                {downloadLoading === template.id ? (
                                  <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                                ) : (
                                  <Download className="h-4 w-4 mr-2 text-gray-700" />
                                )}
                                <span className="text-gray-400 text-xs">
                                  {downloadLoading === template.id
                                    ? "Downloading..."
                                    : "Download"}
                                </span>
                              </button>
                            </div>
                          </div>
                        )}
                      </div>
                    </div>

                    {/* IMAGE (unchanged) */}
                    {template.pageImages &&
                    template.pageImages[0] &&
                    template.pageImages[0] !== "null" &&
                    template.pageImages[0] !== "" &&
                    !template.pageImages[0].includes("placeholder") ? (
                      <div className="w-full h-full flex items-center justify-center">
                        <div className="relative w-full h-full rounded-lg bg-white shadow-sm">
                          <Image
                            width={400}
                            height={600}
                            src={template.pageImages[0]}
                            alt={`${template.title} ${template.docType} Template`}
                            className="object-contain w-full h-full rounded-lg transition-transform duration-300 ease-in-out hover:scale-[1.02]"
                            onError={(e) => {
                              e.currentTarget.style.display = "none";
                              const placeholderDiv =
                                e.currentTarget.parentElement
                                  ?.nextElementSibling;
                              if (placeholderDiv)
                                placeholderDiv.style.display = "flex";
                            }}
                          />
                        </div>
                      </div>
                    ) : null}

                    {/* PLACEHOLDER (unchanged) */}
                    <div
                      className="w-full h-full flex items-center justify-center bg-gradient-to-br from-gray-100 to-gray-200"
                      style={{
                        display:
                          template.pageImages &&
                          template.pageImages[0] &&
                          template.pageImages[0] !== "null" &&
                          template.pageImages[0] !== "" &&
                          !template.pageImages[0].includes("placeholder")
                            ? "none"
                            : "flex",
                      }}
                    >
                      <div className="text-center">
                        <div
                          className={`inline-flex items-center justify-center w-16 h-16 rounded-full text-white text-lg font-bold mb-2 ${
                            template.docType === "RFP"
                              ? "bg-blue-500"
                              : template.docType === "RFI"
                              ? "bg-green-500"
                              : template.docType === "RFQ"
                              ? "bg-purple-500"
                              : template.docType === "NOI"
                              ? "bg-orange-500"
                              : template.docType === "NDA"
                              ? "bg-red-500"
                              : template.docType === "SOW"
                              ? "bg-yellow-500"
                              : "bg-gray-500"
                          }`}
                        >
                          {template.docType || "DOC"}
                        </div>
                        <div className="text-sm text-gray-600 font-medium">
                          {template.docType || "Document"}{" "}
                          {template.isUniversal
                            ? "Universal Template"
                            : "Template"}
                        </div>
                      </div>
                    </div>

                    {/* HOVER OVERLAY (visual only now) */}
                    <div
                      className={
                        `absolute inset-0 bg-black bg-opacity-15 flex items-center justify-center transition-opacity duration-200 z-10 ` +
                        `opacity-100 ` + // Always visible on mobile
                        (hoveredTemplate === index
                          ? "opacity-100"
                          : "md:opacity-0") // Only visible on hover for md+
                      }
                    />
                  </div>

                  {/* demo */}
                </div>
                <div className="mt-1 bg-gray-50 rounded-xl px-0 py-3">
                  <div className="flex items-center justify-between px-0">
                    <h5 className="text-sm font-medium text-gray-900 truncate flex-1 mr-2">
                      {template.title}
                    </h5>
                    <span className="text-xs font-normal text-black bg-blue-200 px-2 py-0.5 rounded-full flex-shrink-0">
                      {template.docType}
                    </span>
                  </div>
                  <p className="text-xs text-gray-500 mt-1 px-0">
                    {template.industry}
                    {template.date && (
                      <>
                        <span className="mx-1 text-gray-400">•</span>
                        {/* <span className="text-gray-500">{template.date}</span> */}
                        <span className="text-gray-500">
                          {formatDateDDMMYYYY(template.date)}
                        </span>
                      </>
                    )}
                  </p>
                </div>
              </div>
            ))}
          </div>
        ) : (
          <div className="space-y-1 md:space-y-0">
            {filteredTemplates.map((template, index) => (
              <div
                key={index}
                className={`flex items-center shadow-sm cursor-pointer ${
                  index % 2 === 1 ? "bg-gray-100" : "bg-white"
                } rounded-md md:rounded-none`}
                onMouseEnter={() => setHoveredTemplate(index)}
                onMouseLeave={() => setHoveredTemplate(null)}
                onClick={() => openModal(template)}
                role="button"
                tabIndex={0}
                onKeyDown={(e) => {
                  if (e.key === "Enter" || e.key === " ") {
                    openModal(template);
                  }
                }}
              >
                <div className="w-16 h-16 md:w-20 md:h-20 flex-shrink-0 overflow-hidden">
                  {template.pageImages &&
                  template.pageImages[0] &&
                  template.pageImages[0] !== "null" &&
                  template.pageImages[0] !== "" &&
                  !template.pageImages[0].includes("placeholder") ? (
                    <img
                      src={template.pageImages[0]}
                      alt="Document Preview"
                      className="object-cover w-full h-full px-2 py-2 border-3 rounded-xl"
                      onError={(e) => {
                        // Hide the broken image and show placeholder instead
                        e.target.style.display = "none";
                        const placeholderDiv = e.target.nextElementSibling;
                        if (placeholderDiv) {
                          placeholderDiv.style.display = "flex";
                        }
                      }}
                    />
                  ) : null}
                  <div
                    className="w-full h-full flex items-center justify-center bg-gradient-to-br from-gray-100 to-gray-200 px-2 py-2 rounded-xl"
                    style={{
                      display:
                        template.pageImages &&
                        template.pageImages[0] &&
                        template.pageImages[0] !== "null" &&
                        template.pageImages[0] !== "" &&
                        !template.pageImages[0].includes("placeholder")
                          ? "none"
                          : "flex",
                    }}
                  >
                    <h1>Pritam Raha</h1>
                    <div
                      className={`inline-flex items-center justify-center w-8 h-8 rounded-full text-white text-xs font-bold ${
                        template.docType === "RFP"
                          ? "bg-blue-500"
                          : template.docType === "RFI"
                          ? "bg-green-500"
                          : template.docType === "RFQ"
                          ? "bg-purple-500"
                          : template.docType === "NOI"
                          ? "bg-orange-500"
                          : template.docType === "NDA"
                          ? "bg-red-500"
                          : template.docType === "SOW"
                          ? "bg-yellow-500"
                          : "bg-gray-500"
                      }`}
                    >
                      {template.docType || "DOC"}
                    </div>
                  </div>
                </div>

                <div className="flex-1 px-3 py-2">
                  <div className="flex items-center">
                    {/* <h5 className="text-sm font-medium text-gray-900 truncate mr-3">
                        {template.title}
                      </h5> */}
                    <h5 className="text-sm font-medium text-gray-900 truncate mr-3">
                      <span className="sm:hidden">
                        {template.title.length > 10
                          ? template.title.slice(0, 8) + "..."
                          : template.title}
                      </span>
                      <span className="hidden sm:inline">{template.title}</span>
                    </h5>
                    <span
                      className={`text-xs font-medium px-3 py-0.5 rounded-full flex-shrink-0 ml-4 ${getDocTypeColor(
                        template.docType
                      )}`}
                    >
                      {template.docType}
                    </span>
                  </div>
                  <p className="text-sm text-gray-500 mt-1">
                    {template.industry}
                  </p>
                </div>

                <div
                  className={`flex-shrink-0 px-3 py-2 relative transition-opacity duration-200 ${
                    hoveredTemplate === index ? "opacity-100" : "opacity-0"
                  }`}
                >
                  <div
                    className="relative"
                    ref={(el) => (menuRefs.current[index] = el)}
                  >
                    <button
                      className="p-1 rounded-full transition-colors"
                      onClick={(e) => toggleMenu(index, e)}
                    >
                      <svg
                        xmlns="http://www.w3.org/2000/svg"
                        height="24px"
                        viewBox="0 -960 960 960"
                        width="24px"
                        fill="#5f6368"
                      >
                        <path d="M480-160q-33 0-56.5-23.5T400-240q0-33 23.5-56.5T480-320q33 0 56.5 23.5T560-240q0 33-23.5 56.5T480-160Zm0-240q-33 0-56.5-23.5T400-480q0-33 23.5-56.5T480-560q33 0 56.5 23.5T560-480q0 33-23.5 56.5T480-400Zm0-240q-33 0-56.5-23.5T400-720q0-33 23.5-56.5T480-800q33 0 56.5 23.5T560-720q0 33-23.5 56.5T480-640Z" />
                      </svg>
                    </button>

                    {activeMenu === index && (
                      <div className="absolute right-0 top-full mt-1 w-30 bg-white border border-gray-200 rounded-lg shadow-lg z-[70]">
                        <div className="py-1">
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("use", template);
                            }}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors"
                          >
                            <FileText className="h-4 w-4 mr-2 text-blue-500" />
                            <span className="text-blue-500 font-medium">
                              Use Template
                            </span>
                          </button>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("edit", template);
                            }}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors"
                          >
                            <Edit className="h-4 w-4 mr-2 text-zinc-700" />
                            Edit
                          </button>

                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("favorite", template);
                            }}
                            disabled={favoriteLoading === template.id}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                          >
                            {favoriteLoading === template.id ? (
                              <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                            ) : (
                              <Heart
                                className={`h-4 w-4 mr-2 ${
                                  isFavorited(template.id)
                                    ? "text-red-500 fill-current"
                                    : "text-zinc-700"
                                }`}
                              />
                            )}
                            {favoriteLoading === template.id
                              ? "Updating..."
                              : isFavorited(template.id)
                              ? "Unfavorite"
                              : "Favorite"}
                          </button>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("delete", template);
                            }}
                            disabled={deleteLoading === template.id}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                          >
                            {deleteLoading === template.id ? (
                              <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                            ) : (
                              <Trash2 className="h-4 w-4 mr-2 text-zinc-700" />
                            )}
                            {deleteLoading === template.id
                              ? "Deleting..."
                              : "Delete"}
                          </button>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("duplicate", template);
                            }}
                            disabled={duplicateLoading === template.id}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                          >
                            {duplicateLoading === template.id ? (
                              <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                            ) : (
                              <Copy className="h-4 w-4 mr-2 text-zinc-700" />
                            )}
                            {duplicateLoading === template.id
                              ? "Duplicating..."
                              : "Duplicate"}
                          </button>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("archive", template);
                            }}
                            disabled={archiveLoading === template.id}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                          >
                            {archiveLoading === template.id ? (
                              <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                            ) : (
                              <Archive className="h-4 w-4 mr-2 text-zinc-700" />
                            )}
                            {archiveLoading === template.id
                              ? "Archiving..."
                              : "Archive"}
                          </button>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              handleTemplateAction("download", template);
                            }}
                            disabled={downloadLoading === template.id}
                            className="flex items-center w-full px-3 py-2 text-xs text-zinc-400 hover:bg-gray-100 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
                          >
                            {downloadLoading === template.id ? (
                              <div className="h-4 w-4 mr-2 animate-spin rounded-full border-2 border-gray-300 border-t-gray-600"></div>
                            ) : (
                              <Download className="h-4 w-4 mr-2 text-zinc-700" />
                            )}
                            {downloadLoading === template.id
                              ? "Downloading..."
                              : "Download"}
                          </button>
                        </div>
                      </div>
                    )}
                  </div>
                </div>
              </div>
            ))}
          </div>
        )}
      </div>

      {/* Template Preview Modal */}
      <TemplatePreviewModal
        isOpen={!!selectedTemplate || isLoadingModal}
        onClose={closeModal}
        template={selectedTemplate}
        isLoading={isLoadingModal}
        error={modalError}
        favoriteLoading={favoriteLoading}
        setFavoriteLoading={setFavoriteLoading}
        toggleFavorite={toggleFavorite}
        isFavorited={isFavorited}
        router={router}
        goToEditor={goToEditor}
      />

      {/* Delete Confirmation Modal */}
      <Dialog open={showDeleteModal} onOpenChange={setShowDeleteModal}>
        <DialogContent className="z-[80] sm:max-w-[425px]">
          <DialogHeader>
            <DialogTitle>Delete Template</DialogTitle>
            <DialogDescription>
              {`Are you sure you want to delete "${
                templateToDelete?.title?.length > 28
                  ? templateToDelete?.title.slice(0, 25) + "..."
                  : templateToDelete?.title
              }"?`}
              <br /> Template will still be available in Trash.
              <br />
              <span className="text-red-600 font-medium">
                {/* Template moved to Trash. */}
              </span>
            </DialogDescription>
          </DialogHeader>
          <DialogFooter className="flex gap-2">
            <Button
              variant="outline"
              onClick={handleCancelDelete}
              className="flex-1"
              disabled={deleteLoading === templateToDelete?.id}
            >
              Cancel
            </Button>
            <Button
              onClick={handleConfirmDelete}
              disabled={deleteLoading === templateToDelete?.id}
              className="flex-1 bg-red-600 hover:bg-red-700 text-white disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {deleteLoading === templateToDelete?.id ? (
                <>
                  <div className="mr-2 h-4 w-4 animate-spin rounded-full border-2 border-white border-t-transparent" />
                  Deleting...
                </>
              ) : (
                <>
                  <Trash2 className="mr-2 h-4 w-4" />
                  Delete
                </>
              )}
            </Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>

      {/* Archive Confirmation Modal */}
      <Dialog open={showArchiveModal} onOpenChange={setShowArchiveModal}>
        <DialogContent className="z-[80] sm:max-w-[425px]">
          <DialogHeader>
            <DialogTitle>Archive Template</DialogTitle>
            <DialogDescription>
              {`Are you sure you want to archive "${
                templateToArchive?.title?.length > 28
                  ? templateToArchive?.title.slice(0, 25) + "..."
                  : templateToArchive?.title
              }"?`}
              <br /> Template will be moved to Archive and can be Unarchived
              later.
              <br />
              <span className="text-blue-600 font-medium">
                {/* Template moved to Archive. */}
              </span>
            </DialogDescription>
          </DialogHeader>
          <DialogFooter className="flex gap-2">
            <Button
              variant="outline"
              onClick={handleCancelArchive}
              className="flex-1"
              disabled={archiveLoading === templateToArchive?.id}
            >
              Cancel
            </Button>
            <Button
              onClick={handleConfirmArchive}
              disabled={archiveLoading === templateToArchive?.id}
              className="bg-orange-500 hover:bg-orange-600 text-white"
            >
              {archiveLoading === templateToArchive?.id
                ? "Archiving..."
                : "Archive"}
            </Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>
    </div>
