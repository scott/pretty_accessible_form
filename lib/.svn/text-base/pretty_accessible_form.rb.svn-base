require "form_builder_helper"

# Build a form after Pretty Accessible Forms
module PrettyAccessibleForm

  class PAFormBuilder < ActionView::Helpers::FormBuilder

    ActionView::Helpers::AssetTagHelper.register_javascript_include_default("jquery.js", "inline_mozilla_hack.js")

    (field_helpers - %w(check_box radio_button) + %w(select)).each do |selector|
      src = <<-END_SRC
        def #{selector}(field, options = {})
          field = field.to_s
          label_text, required = extract_paf_options(field, options)
          generic_field(field, super, label_text)
        end
        END_SRC
        class_eval src, __FILE__, __LINE__
      end

      %w(check_box radio_button).each do |selector|
        src = <<-END_SRC
       def #{selector}(field, options = {})
         field = field.to_s
         label_text, required = extract_paf_options(field, options)
         generic_field(field, super, label_text, :required => required, :label => :after)
       end
       END_SRC
       class_eval src, __FILE__, __LINE__
     end

     def submit(text, options = {})
       generic_field(nil, @template.submit_tag(text, options))
     end

     def file_column_field(field, options = {})
       field = field.to_s
       label_text, required = extract_paf_options(field, options)
       generic_field(field, @template.file_column_field(@object_name, field, options), label_text, :required => required)
     end

     def separator(new_section_name)
       <<-HTML
        </table></fieldset>
        <fieldset><legend>#{new_section_name}</legend><table>
        HTML
     end

     protected
     def generic_field(fieldname, field, label_text = nil, options = {})
       required = options[:required] ? span('*', :class => 'requiredField') : ''
       unless label_text.blank?
         if options[:label] == :after
           li(field + label(label_text, "#{@object_name}_#{fieldname}", true) + required)
         else
           li(
              label(label_text, "#{@object_name}_#{fieldname}") +
              field + required
              )
         end
       else # No label
         li(field + required)
       end
     end

     def li content, options = {}
       @template.content_tag 'li', content, options
     end

     def label text, for_field, after = false
       @template.content_tag 'label', "#{text}#{after ? '' : ':'}", :for => for_field
     end

     def extract_paf_options field, options
       label_text = options.delete(:label) || field.to_s.humanize
       required = options.delete(:required) || false
       [label_text, required]
     end
   end

   def pa_form_for(object_name, *args, &proc)
     options = args.last.is_a?(Hash) ? args.last : {}
     if options[:html].nil? then
       options[:html] = { :class => "cmxform" }
     else
       options[:html][:class] = (options[:html][:class].nil?) ? "cmxform" : "#{options[:html][:class]} cmxform"
     end
     legend = options.delete :legend
     if legend.blank?
       prefix = "<fieldset><ol>"
       postfix = "</fieldset></ol>"
     else
       prefix = "<fieldset><legend>#{legend}</legend><ol>"
       postfix = '</ol></fieldset>'
     end

     custom_form_for(
                     PAFormBuilder, prefix, postfix,
                     form_tag(options.delete(:url) || {}, options.delete(:html) || {}),
                     object_name, *args, &proc)
   end

   def pa_remote_form_for(object_name, *args, &proc)
     options = args.last.is_a?(Hash) ? args.last : {}
     if options[:html].nil? then
       options[:html] = { :class => "cmxform" }
     else
       options[:html][:class] = (options[:html][:class].nil?) ? "cmxform" : "#{options[:html][:class]} cmxform"
     end
     legend = options.delete :legend
     if legend.blank?
       prefix = "<fieldset><ol>"
       postfix = "</ol></fieldset>"
     else
       prefix = "<fieldset><legend>#{legend}</legend><ol>"
       postfix = '</ol></fieldset>'
     end
     custom_form_for(PAFormBuilder, prefix, postfix, form_remote_tag(options), object_name, *args, &proc)
   end
 end


