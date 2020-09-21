# Summary
Filtering is an existing feature that's used in a few different locations. We use a homebrew solution instead of gems like `Ransack` for now, though if it made more sense to switch to `Ransack` that could be raised with the team. The text below will outline how the Filtering feature works.

One need in filtering is that the user is able to apply multiple filters at once, to constrain their resultset and drill-down (effectively doing an intersection of all the subsets). Any replacement should ensure that it is able to do that.

# Filterable
The feature is driven primarily by the `Filterable` concern (`app/models/concerns/filterable.rb`), added to a model. This affords it a `:class_filter` method, which applies a collection of filter parameters. Filtering involves three layers: 

 - The model layer provides scopes / class methods that will be the filter that is applied
 - The controller indicates which filters it accepts and actually applies the filters
 - The view layer presents the UI to the user about what filters can be applied, as well as giving status about which ones have already been applied

All three layers must be touched to actually add new filters, but in most cases you can get by with copy-pasting existing stuff.

## Model
The convention is to put all the scopes intended for filtering directly underneath the `include Filterable` line, to show the intention there. A typical scope will look like this:

```ruby
# app/models/donation.rb
scope :at_storage_location, ->(storage_location_id) {
    where(storage_location_id: storage_location_id)
}
```

Name the scope in a way that suggests how it's filtering the resource. It will generally accept a single argument, and mutate via a `where`.

## Controllers
Use a separate params hash for filtering, rather than adding to the resource params. This keeps from polluting the existing params as well as allowing you to keep them simplified.

```ruby
# app/controllers/donations_controller.rb
def filter_params
    return {} unless params.key?(:filters)

    params.require(:filters).slice(:at_storage_location, :by_source, :from_donation_site, :by_diaper_drive, :by_diaper_drive_participant, :from_manufacturer)
  end
```

The convention is to call it `filter_params`. The guard clause is necessary, unless you are :100: you will always need the filter to be applied and you want it to bomb without one.

The convention for the required key is `:filters`. The params list should list out symbolized versions of the scope(s) you created in the model. (`:at_storage_location` mirroring the scope `-> at_storage_location`).

You'll also need to actually apply the filter to the collection. This is *typically* done in `#index` but there's no reason you couldn't use it elsewhere if you needed to.

```ruby
# app/controllers/donations_controller.rb#index
@donations = current_organization.donations
                .includes(:line_items, :storage_location, :donation_site, :diaper_drive, :diaper_drive_participant, :manufacturer)
                .order(created_at: :desc)
                .class_filter(filter_params)
                .during(helpers.selected_range)
```

The `.class_filter(filter_params)` is the relevant line. The rest are incidental. You can just drop that one line in verbatim. 

If you're doing a drop-down select, you'll also want to pre-load an instance variable here with that data:
```ruby
   @storage_locations = @donations.collect(&:storage_location).compact.uniq.sort
```
That one is a bit elaborate (it was constraining the set to only show storage locations that were used in donations). Doing `current_organization.storage_locations` or similar is often fine. Be sure it's scoped to organization though!

And setting a different one with their selection lets you pre-select their selection on page load:
```ruby
   @selected_storage_location = filter_params[:at_storage_location]
```

## Views
The filters are generally done in a separate block at the top. There was a convention of using `id="filters"` for the container, but that has been left off during re-designs. Ideally it should have that, though, so that it's easier to hook onto with JS and tests.

```ruby 
<%= form_tag(donations_path, method: :get) do |f| %>
              <div class="row"> <!-- this is template-dependent -->
                <% if @storage_locations.present? %>
                  <div class="form-group col-lg-3 col-md-4 col-sm-6 col-xs-12"> <!-- this too -->
                    <%= label_tag "by Storage Location" %>
                    <%= collection_select(:filters, :at_storage_location, @storage_locations || {}, :id, :name, {include_blank: true, selected: @selected_storage_location}, class: "form-control") %>
                  </div>
                <% end %>
              <!-- ... -->
              </div>
    <!-- ... -->
<% end %>
```

The key line there is the collection select:

```ruby
<%= collection_select(:filters, :at_storage_location, @storage_locations || {}, :id, :name, {include_blank: true, selected: @selected_storage_location}, class: "form-control") %>
```

The first 2 parameters ensure it goes to the correct params hash. The third parameter is the filterable selections.

You can use essentially any kind of field and scope combination, for this, not just collection selects.